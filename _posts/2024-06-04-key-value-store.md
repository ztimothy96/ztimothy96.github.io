---
layout: post
title: "Designing a key-value-store"
subtitle: "System design interviews, chapter 6"
thumbnail-img: /assets/img/system-design/swe.png
share-img: /assets/img/system-design/swe.png
tags: [software engineering]
---
I'm re-reading *System Design Interview: An Insider's Guide* by Alex Xu. Here's Chapter 6 on how to design a key-value store.

## Understand the problem
A key-value store is a kind of NoSQL database that functions as a distributed hash table: it stores a map from unique strings ("keys") to associated values. Given a key, we should be able to quickly insert, retrieve, or remove entries from the database.

Unsurprisingly, there are tradeoffs. For instance, we can optimize our database for reads, writes, and memory. Now would also be a good time to mumble something about the CAP theorem and how we have to choose between consistency, availability, and partition tolerance. Here are some example requirements, which may differ depending on your use case.

**Functional requirements**:
- Put: puts a key-value pair inside the store.
- Get: given an input key, gets its associated value from the store.

**Non-functional requirements**:
- Entry size: the key-value pair is small (< 10 KB).
- High scalability: the database should be able to store lots of data (basically unlimited scaling).
- High availability: we should be able to use the database anytime.
- Low latency.
- Automatic scaling: can add or delete servers by traffic.
- Tunable consistency: we can change how much consistency we want without completely restructuring the database.

## High-level design

Here I'm just going to plop down a few diagrams and move on. This chapter is not really about high-level system design, but more about the internal details of how databases work.

### Data partition, replication

We have a lot of data and can't fit it on a single computer. So we need to solve the data partition: how do we allocate our data evenly across many server clusters? Fortunately, some smart mathematicians like David Karger invented an algorithm called consistent hashing to solve this very problem... hooray for computer science!

But if we put a piece of data on a single server, that can catch on fire, and our data would be destroyed. So we also need to replicate data. We could allocate the same piece of data to multiple servers (replicas) by going around our consistent hash ring and just picking the first $$N$$ servers that have space for it, where $$N$$ is the desired number of replicas. Even better, we could replicate our data across geographically separated data centers and use consistent hashing for each data center. That way, if one data center goes down, the others might remain intact.

There isn't much to say about the architecture here; it's just a client connected to a bunch of decentralized servers. Each piece of data is stored on several replicas. The complicated part comes from getting the servers to coordinate with each other.

![6-17](/assets/img/system-design/6-17.png){: .mx-auto.d-block :}


### Reads and writes

Writing: first put the key-value pair into a cache (we'll want it to speed up read operations later). If the cache overflows, then we have to evict values and write them to disk storage. Pretty simple, right?

![6-19](/assets/img/system-design/6-19.png){: .mx-auto.d-block :}

Reading:
Try to first read from the cache. If we can't find the key there, then we would have to find the right disk to read from. Since there are too many keys to fit in a regular hash table, we'd use a Bloom table to find the replicas. Then we retrieve the value and return it to the user.

![6-21](/assets/img/system-design/6-21.png){: .mx-auto.d-block :}


## Detailed design
We took our data and partitioned and replicated it across a bunch of servers. Now we have to figure out how all these decentralized servers are going to maintain consistency and handle failures. What fun...

### Consistency

#### Creating consistency: quora
Now that we have the same data stored on several replicas, we need to make sure that they stay consistent: they should store copies of the same data, even after updates. A database is *strongly consistent* if a read operation always sees the latest updated version of data. A database is *eventually consistent* if the most recent version propagates to all the replicas over time.

When a server receives a read/write request, it has to propagate that information to other replicas. However, we probably don't want to wait for acknowledgment from all the other replicas; that would take too long. Instead, we can move on once we get a *quorum consensus* (agreement from a set number of the other replicas). All we have to do is specify some parameters.
- $$N$$: number of replicas
- $$W$$: write quorum. We need at least $$W$$ replicas to acknowledge a write before the operation succeeds.
- $$R$$: read quorum. We need to read the same data from $$R$$ replicas before the operation succeeds.

If $$W + R > N$$, then the database will achieve strong consistency. (Think of the pigeonhole principle.) However, making a lot of reads and writes will slow down operations. Picking different values of $$W, R$$ will give different tradeoffs between consistency and write/read performance. 

**Exercise**. Play around with different values of $$W$$, $$R$$, $$N$$ and think about what happens at the edge cases. When might you want to use each one?

#### Resolving inconsistency: vector clocks
When storing different versions of a data object on different servers, we'd like a way to label their versions so that we can detect inconsistencies and resolve conflicts quickly. One such labeling scheme is called *vector clocks.* (There are other conflict resolution algorithms, which I might find mathematically interesting; see the Wiki page on [logical clocks](https://en.wikipedia.org/wiki/Logical_clock).) 

Conceptually, a vector clock is just a giant vector $$(v_1, v_2, ..., v_N)$$ where the $$i$$-th component is the number of versions that server $$i$$ has written to this data object. Each of the servers will store its own vector clock for a piece of data, $$D(i)$$. If a server $$i$$ reads the data from server $$j$$, it will also receive the associated vector clock $$D(j)$$. If $$D(i) \geq D(j)$$ (where the inequality holds component-wise), then we know that server $$i$$ has seen all the updates that server $$j$$ has, so it stores a more recent version. Similarly, if $$D(i) \leq D(j)$$, then server $$j$$ has the recent version. Otherwise, if neither inequality holds, then we have detected a version conflict; each version has some updates from servers that the other doesn't have.

It would be silly to store version numbers from every single replica when implementing a vector clock in practice. However, I find my explanation much easier to understand rather than working through a bunch of disjointed examples. What can I say... I'm still a math person at heart. But most of the interesting algorithms in distributed computing were probably discovered many decades ago; good for the world, too bad for me.

### Failures

#### Detecting failure: gossip
How do we know when a server isn't working anymore? Just having one server repeatedly ping another isn't reliable, because the connection between them could fail, or maybe both could go down at once, etc. We could try multicasting, but that would bog down the network. Instead, we can check on server health using the *gossip protocol.*

- Every node has a list of other node IDs and heartbeat counters.
- Each node periodically updates its heartbeat and sends its heartbeat list to other random nodes.
- When receiving a heartbeat, nodes update their heartbeat list to the latest counter.
- If a node's heartbeat hasn't increased in a long time, the group collectively agrees that it has failed.

This protocol is rather clever in that it propagates information about all nodes around the group, without having everyone talk to each other at once. (The nodes randomly meet in small groups and "gossip" about everybody else.) This allows us to coordinate failure detection while maintaining low latency.

#### Fixing failures: handoffs and Merkle trees
If there is a temporary failure, we can revive the failing server and try to restore its data. One way is to use a *hinted handoff* protocol: while the replica is down, we assign another to take its place and take care of read and write operations. In addition, this temporary new replica stores a "hint" containing the ID and metadata about the failing server, and pings the old server once in a while. When it comes back online, the new server can share the updated data.

If the failing server doesn't revive after several hours, it might be permanently dead. In this case, we might want to sync up the data on all the remaining replicas before picking a new one. However, it might take a while to search large data files for inconsistencies. One way to find conflicts quickly is by using a *Merkle tree* (or *hash tree*). As the name suggests, the tree recursively stores hashes of the data.

- Construction: we recursively divide up the data into blocks, run hash functions on each block, and store those hashes in a tree.
- Query: given two pieces of data, we want to find possible conflicts. Instead of directly comparing the data, we compare the hashes in their Merkle trees. This way we can perform a tree search to find the root nodes with conflicting hashes; these correspond to blocks of data with conflicting values.
- Complexity: the tree uses $$O(n)$$ space and $$O(\log n)$$ query time, where $$n$$ is the length of your data. Simple and sweet.

If the entire data cluster fails, there is not much you can do to restore the data. For instance, if somebody bombs a city and burns up all of your computers there, it's game over. Hopefully, you already backed up your data on replicas somewhere else...

## Look back
Well, it doesn't seem so hard to remember the key points.
- To get scalability, availability, and fault tolerance, we partition and replicate the data.
- To get consistency and read/write tradeoffs, use quorum consensus. 
- To detect and resolve inconsistent data, use vector clocks.
- To detect server failures, use a gossip protocol.
- To fix temporary failures, use hinted handoff. To synchronize servers after a permanent failure, use Merkle (hash) trees.
