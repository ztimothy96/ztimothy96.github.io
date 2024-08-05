---
layout: post
title: "Designing ID generators"
subtitle: "System design interviews, chapter 7"
thumbnail-img: /assets/img/system-design/swe.png
share-img: /assets/img/system-design/swe.png
tags: [software engineering]
---
I'm re-reading *System Design Interview: An Insider's Guide* by Alex Xu. Here's Chapter 7 on how to design a unique ID generator.

## Understand the problem
In designing software systems, it's often very helpful to generate a bunch of unique IDs for objects (for instance, so that we can generate keys when we put those objects into a database). However, a single server won't be able to handle a giant stream of requests flooding in, and besides we want to avoid single points of failure, and all that other fluff. So we should think of a distributed solution.

**Functional requirements**:
- Generate IDs.
- IDs must be numeric, unique, fit into 64 bits, and be ordered by date.
- We should be able to generate 10,000 IDs per second.

**Non-functional requirements**:
- *Fault-tolerant*: the system should keep working even if a few servers go down.
- *Availability*: the system should have no downtime.

## High-level design

Several approaches we could take, including multi-master replication, UUIDs, and Twitter snowflake. Let's take a look at each of these in turn.

### Multi-master replication

If we have $$N$$ servers, then we can have each server $$i$$ generate all the IDs that are congruent to $$i \pmod N$$. Each time a server generates an ID, it increments by $$N$$. This yields unique IDs but doesn't scale when we want to add new servers; let's move on.

### UUID

Since I'm a computer scientist at heart, UUIDs (universally unique identifiers) are my favorite approach: randomly select a 128-bit number and hope that all your selections are unique. You can do some quick maths to show that collisions are so rare they will never occur in practice. This method is extremely easy to implement, and it scales limitlessly!

However, let's go back to being software engineers and say we want IDs that are increasing by date and only take up 64 bits instead of 128.

### Twitter snowflake
To squeeze more out of each bit, we need to design a custom ID generation scheme. We can split the 64 bits into chunks that encode different information. For example, Twitter uses 41 bits for the timestamp, 5 bits for datacenter ID, 5 more bits for machine ID, and 12 bits for a sequence number. The sequence number is a counter for all the IDs that we generate within a single timestamp, so it resets every millisecond. That way we can handle $$2^{12} = 4096$$ per millisecond.

Note that the 41 timestamp bits allow us to run the service for $$2^{41}$$ milliseconds, or roughly $$70$$ years before we have to think of a new approach. Not bad. The big problem with the Twitter snowflake approach is that we have to be very careful about assigning new IDs to data centers or machines, as that could screw up the numbering and create collisions.

There's not much to say in terms of detailed design or looking back. Generating IDs is not much of a system question, in my opinion, and I'm confused as to why it's in this book. What a short chapter!
