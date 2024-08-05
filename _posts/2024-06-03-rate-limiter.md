---
layout: post
title: "Designing rate limiters"
subtitle: "System design interviews, chapter 4"
thumbnail-img: /assets/img/system-design/swe.png
share-img: /assets/img/system-design/swe.png
tags: [software engineering]
---
I have found the motivation to become an excellent software engineer, which means I will probably get there pretty soon. (I have a track record of becoming a top 5% performer in basically everything I have ever worked hard at.) The motive itself I will keep a secret, but let's just say it is not something that anyone else has ever suggested to me. To those of you who believed in me, thank you. To those of you who didn't: meh, you were wrong.

I'm re-reading *System Design Interview: An Insider's Guide* by Alex Xu. Since I don't want to repeatedly comb through the 300-page book every time I review concepts, I will take notes. These will be rather sparse and are certainly not a substitute for reading the book in full detail. Here's Chapter 4 on how to design a rate limiter.

## Understand the problem
Say you have a software service where the users can send lots of requests. If people send too many requests at once, that can create problems.
- *Denial-of-Service (DoS) attack*: the requests could overload the system and make it crash.
- *Increased costs*: if you have to pay money for each request (e.g. calling another API like ChatGPT), a flood of requests can raise operating costs.

That's why we might want to create a rate limiter to prevent request overloads. A rate limiter is a software system that limits the number of requests the service receives in a time frame. The extra requests are "throttled," or ignored by the system.

**Functional requirements**:
- Limit requests.
- Handle exceptions: the rate limiter should inform users when their requests have been throttled.

**Non-functional requirements**:
- *Fault-tolerant*: the system should keep working even if the rate limiter goes down.
- *Low latency*: the rate limiter should not slow down system performance.
- *Low memory*: the rate limiter should not consume massive amounts of memory.

## High-level design

### Where to put the rate limiter?
Assume a simple client-server architecture where clients make requests to the servers, and the servers return the requested data. We can choose to put the rate limiter on the clients, the servers, or in the middle. As usual, there are trade-offs for this decision.
- *Client-side*: easier to write, but difficult to enforce. A malicious actor can get around client-side rate limiters by opening many different clients.
- *Server-side*: easier to enforce, but difficult to write. There's no way for the client to prevent the server from simply rejecting extra requests. However, we have to build the whole thing.
- *Middleware*: makes the rate limiter into a separate service, managed by a third party. We might not have to build the rate limiter ourselves, but we also have less control over the implementation.

Since client-side rate limiters have trouble fulfilling the functional requirements, we usually want to put the rate limiter on the servers or in middleware. Large companies with giant budgets and engineering teams prefer to build custom rate limiters, while smaller companies tend to use existing middleware solutions.

### Algorithms
Right now, I'm trying to focus on system design and not algorithms, so I'll satisfy myself with only a brief overview of rate-limiting approaches.
- *Token bucket*: keep a bucket of tokens representing requests that can be fulfilled. Every time we accept a request, we remove a token from the bucket. When we run out of requests, we start throttling requests. The bucket refills tokens at a constant rate, and it discards extra tokens if there is overflow past the bucket capacity.
- *Leaking bucket*: like the token bucket algorithm, except the requests are processed at a constant rate. (Tokens "leak" from the bucket.) The performance is more stable, but a traffic burst can flood the bucket with old requests.
- *Fixed window counter*: divide time into windows of fixed length and count how many requests we receive in each window, throttling requests that go over the limit. Super easy to implement, but can't prevent traffic spikes at the edges of time windows.
- *Sliding window log, sliding window counter*: exactly what they sound like. They use timestamp lookups to solve the traffic spike problem with the fixed window counter algorithm at the cost of using extra memory. I'm not going to talk about the implementation details here.

All of these approaches have the same basic idea: set a limit for how many requests we can fulfill, keep a counter of how many requests we've received, and throttle all the requests that go over the limit. To store the counter, a cache (like Redis) is ideal because it doesn't require too much memory and it can keep latency low.

## Detailed design
A basic rate limiter is pretty simple to design: store the rate-limiting rules and the counter in some caches. Users send requests to middleware, which fetches the rules and counters and decides whether to throttle. If the request goes through, we feed it to the API servers. If it is throttled, we can drop the request or store it in a queue for processing later, and then we'll return an HTTP error code to the client.

![4-13](/assets/img/system-design/4-13.png){: .mx-auto.d-block :}

However, there are more problems to think about in distributed systems: race conditions, synchronization, and monitoring.
- *Race conditions*: two servers can simultaneously fetch the counter, increment, and write back. The result is that the counter only increments by one when it should have incremented by two. Fortunately, the Redis counter (sorted sets) has a built-in ability to handle race conditions. How does it work?
- *Synchronization*: if we have multiple rate-limiting servers, we might want them to remember information about specific clients (like how many requests that client has sent in the past minute). However, the client can send requests to different servers. Instead of storing client states on the servers, we could again look them up from a Redis cache.
- *Monitoring*: we'd like to be able to monitor system performance and adjust when problems arise. For instance, if there's a traffic spike, maybe we should set our rate-limiting algorithm to the token bucket. What should we pick for the bucket capacity and the refill rate?

## Look back

We fulfilled functional requirements by outsourcing the problem to existing middleware, and we fulfilled non-functional requirements by picking appropriate components to store information in our system. The main idea is to use a cache, because the counters expire at regular time intervals, and caches are exactly what we want to use to store this kind of temporary data.
- *Fault tolerance*: replicate the rate-limiting servers.
- *Low latency*: keep rules and counters in a cache.
- *Low memory*: pick a simpler rate-limiting algorithm that does not require us to look up timestamps. We also eject counts from the cache after a fixed time window expires.

Given more time, we can think about how to design apps that discourage clients from sending excessive requests. Other than that, maybe I should go back and look in detail at how race conditions and synchronization are handled in Redis. The design of the rate limiter itself is pretty straightforward, however.

(By the way: chapter 5 is just a recap of how consistent hashing works. I think I'm going to skip this since I get the general idea, and I don't consider it system design proper.)
