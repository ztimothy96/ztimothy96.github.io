---
layout: post
title: "Designing a key-value-store"
subtitle: "System design interviews, chapter 6"
thumbnail-img: /assets/img/system-design/swe.png
share-img: /assets/img/system-design/swe.png
tags: [software engineering]
---
I'm re-reading *System Design Interview: An Insider's Guide* by Alex Xu. Here's Chapter 6 on how to design a key-value store. In actuality, this chapter is really about how distributed databases work internally. It's highly important to understand these concepts because nearly every application needs to store data somewhere, so picking the right kind of database can help us fulfill nonfunctional requirements.

## Understand the problem
A key-value store is a kind of NoSQL database that functions as a distributed hash table: it stores a map from unique strings (called "keys") to associated values. Given a key, we should be able to quickly insert, retrieve, or remove entries from the database.

Unsurprisingly, there are tradeoffs. For instance, we can optimize our database for reads, writes, and memory. Now would also be a good time to mumble something about the CAP theorem and how we have to choose between consistency, availability, and partition tolerance. Here are some example requirements, which may differ depending on your use case.

**Functional requirements**:
- *Put*: we can put a key-value pair inside the store.
- *Get*: given an input key, we can get its associated value from the store.

**Non-functional requirements**:
- *Entry size*: the key-value pair is small (< 10 KB).
- *High scalability*: the database can store lots of data (basically unlimited scaling).
- *High availability*: we can access the database anytime.
- *Low latency*: we can access entries quickly.
- *Automatic scaling*: we can add or delete servers by traffic.
- *Configurable consistency*: we can change how much consistency we want without completely restructuring the database.

## High-level design

Here I'm just going to plop down a few diagrams and skip to low-level design. This chapter is not really about high-level system design, but more about the internal details of how databases work.

### Partition and replication

Rule of thumb: to get scalability, partition your data. To get fault tolerance, replicate your data.

Suppose we have a lot of data and can't fit it on a single computer. Then we'll have to partition the data, distributing it across many servers. At first, it might seem tricky to allocate our data evenly, while allowing flexible scaling when we want to add or remove servers from our clusters. Fortunately, the computer scientist David Karger invented consistent hashing to solve this very problem. (It turns out that algorithms aren't just toy problems; they're used in basically every scalable distributed database...)

But if we put a piece of data on a single server, it could be lost forever when that server catches on fire. So we also need to replicate data by storing copies on multiple servers, or *replicas*. We could go around our consistent hash ring and pick the first few servers that have space for it. Even better, we could replicate our data across geographically separated data centers and use consistent hashing for each data center. That way, if one data center goes down, the others might remain intact.

The architecture here is simple: it's just a client connected to a bunch of decentralized servers. Each piece of data is stored on several replicas. Getting the servers to coordinate with each other is more complicated, however.

![6-17](/assets/img/system-design/6-17.png){: .mx-auto.d-block :}


### Reads and writes

*Reading*: Try to first read from the cache. If we can't find the key there, then we would have to find the right disk to read from. Since there are too many keys to fit in a regular hash table, we'd use a Bloom table to find the replicas. Then we retrieve the value and return it to the user.

![6-21](/assets/img/system-design/6-21.png){: .mx-auto.d-block :}

*Writing*: first put the key-value pair into a cache. If the cache overflows, then we have to evict values and write them to disk storage. Pretty simple, right?

![6-19](/assets/img/system-design/6-19.png){: .mx-auto.d-block :}


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
When storing different versions of a data object on different servers, we'd like a way to label their versions so that we can detect inconsistencies and resolve conflicts quickly. In practice, the most popular labeling scheme is the *vector clock.*

Conceptually, a vector clock is just a giant vector $$(v_1, v_2, ..., v_N)$$ where the $$i$$-th component is the number of versions that server $$i$$ has written to a data object. Let $$D$$ be a data object; each server $$i$$ will store a local vector clock $$D(i)$$ for that data. (In practical implementations, the data will not be stored on all $$N$$ servers in the cluster, but I find that it's easier to understand why conflict resolution works if we forget about such details.)

To perform conflict resolution, a server $$i$$ reads the data from server $$j$$, receives its vector clock $$D(j)$$, and compares this with its own. If $$D(i) \geq D(j)$$ (where the inequality holds component-wise), then we know that server $$i$$ has seen all the updates that server $$j$$ has, so it stores a more recent version. Similarly, if $$D(i) \leq D(j)$$, then server $$j$$ has the recent version. Otherwise, if neither inequality holds, then we have detected a version conflict; each version has some updates from servers that the other doesn't have.

There are many conflict resolution algorithms, which I might find mathematically interesting; see the Wiki page on [logical clocks](https://en.wikipedia.org/wiki/Logical_clock). In a parallel universe, it would have been fun to design some distributed databases and discover the algorithms that make them work. But most of the interesting mathy work in distributed computing was completed many decades ago; great for the world, too bad for me.

### Failures

#### Detecting failure: gossip
How do we know when a server isn't working anymore? Having one server repeatedly ping another isn't reliable because the pinger becomes a single point of failure. The connection between the servers could fail, both could go down at once, etc. We could try multicasting, but that would bog down the network. Instead, we can check on server health using the *gossip protocol.* At a high level, nodes randomly meet in small groups and "gossip" about everybody else.

- Every node has a list of other node IDs and heartbeat counters.
- Each node periodically updates its heartbeat and sends its heartbeat list to other random nodes.
- When receiving a heartbeat, nodes update their heartbeat list to the latest counter.
- If a node's heartbeat hasn't increased in a long time, the group collectively agrees that it has failed.

This protocol is rather clever in that it propagates information about all nodes around the group without forcing everyone to talk to each other at once. This allows us to coordinate failure detection while maintaining low latency. Look at that, more algorithms that are useful in practice!

#### Fixing failures: handoffs and Merkle trees
If there is a temporary failure, we can revive the failing server and try to restore its data. One way is to use a *hinted handoff* protocol: while the replica is down, we assign another to take its place and take care of read and write operations. In addition, this temporary new replica stores a "hint" containing the ID and metadata about the failing server, and pings the old server once in a while. When it comes back online, the new server can share the updated data.

If the failing server doesn't revive after several hours, it might be permanently dead. In this case, we might want to sync up the data on all the remaining replicas before picking a new one. However, it might take a while to search large data files for inconsistencies. One way to find conflicts quickly is by using a data structure called the *Merkle tree* (or *hash tree*). As the name suggests, the tree recursively stores hashes of the data.

- *Construction*: we recursively divide up the data into blocks, run hash functions on each block, and store those hashes in a tree.
- *Query*: given two pieces of data, we want to find possible conflicts. Instead of directly comparing the data, we compare the hashes in their Merkle trees. This way we can perform a tree search to find the root nodes with conflicting hashes; these correspond to blocks of data with conflicting values.
- *Complexity*: the tree uses $$O(n)$$ space and $$O(\log n)$$ query time, where $$n$$ is the length of your data. Simple and sweet.

If the entire data cluster fails, there is not much you can do to restore the data. For instance, if somebody bombs a city and burns up all of your computers there, it's game over. Hopefully, you already backed up your data on replicas somewhere else...

## Look back
Well, it doesn't seem so hard to remember the key points.
- To get scalability, availability, and fault tolerance, we partition and replicate the data.
- To get consistency and read/write tradeoffs, use quorum consensus. 
- To detect and resolve inconsistent data, use vector clocks.
- To detect server failures, use a gossip protocol.
- To fix temporary failures, use hinted handoff. To synchronize servers after a permanent failure, use Merkle (hash) trees.
- To look up key-value pairs when you have way too many keys for a hash table, use a Bloom filter.

Funnily enough, most of this chapter was not about system design, but rather about data structures and algorithms. I feel that database internals are a bit like Leetcode interviews: it's good to know about the mathematical concepts, but nobody uses this stuff in their day-to-day engineering work. This is not because the low-level details are useless, but rather because they are so useful that they have already been highly optimized, and most engineers can now import them without worrying about them.

That situation may change if you are a hard-core engineer building the latest vector databases like [Pinecone](https://www.pinecone.io/learn/vector-search-basics/), in which case maybe there's still some opportunity to think about math problems. Well, I wouldn't know, as I don't work for them...
