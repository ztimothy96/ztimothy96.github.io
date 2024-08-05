---
layout: post
title: "More on onion rings"
thumbnail-img: /assets/img/algorithms.png
share-img: /assets/img/algorithms.png
tags: [math, algorithms]
---

Last week, I asked a question about how to pack onion rings into concentric circular stacks to minimize their total area, and I noted that this problem was equivalent to solving an interval scheduling problem with a different objective function. After a little more thought, I ended up solving the problem completely.

> **Problem.** Given a set of $$n$$ intervals, an interval scheduling of this set is a partition of the intervals to machines such that intervals on the same machine do not overlap. The *area* of a scheduling is $$\sum_j f_j^2$$, where $$f_j$$ is the maximum finish time over all intervals on machine $$j$$. Find an interval scheduling with minimum area.

For example, consider the following example.
![intervals2](/assets/img/intervals2.png){: .mx-auto.d-block :}

We would want to schedule $$I_1$$ and $$I_2$$ on the same machine, and $$I_3$$ separately. Since $$I_3$$ finishes earlier than $$I_2$$, we would like it to be the latest interval scheduled on machine 2. This problem differs from the one I considered in the previous post (minimizing the number of machines), where it wouldn't matter whether we swapped $$I_3$$ and $$I_2$$. 

After scheduling the interval with the latest finish time, we would like to pack many of the late-finishing intervals onto that same machine, so that their finish times won't contribute to the area. This intuition suggests that we use a greedy algorithm similar to that discussed previously, except now we order the intervals decreasing by finish time. Let's call this algorithm "greedy-by-decreasing-finish."

Recall the theorem we saw last time:

> **Theorem.** The greedy-by-earliest-start algorithm finds an interval scheduling using the minimum number of machines.

If we run the same algorithm, but first pack the intervals which finish late, then we can ensure that each machine we add contributes as little area as possible.

> **Theorem.** For any any feasible solution, the $$k$$-th latest finish time of its machines is lower-bounded by that of the greedy-by-decreasing-finish algorithm.

> **Proof.** Let interval $$i$$ be the latest one scheduled on machine $$k$$. Since we added the intervals greedily by decreasing finish time, interval $$i$$ must conflict with $$(k-1)$$ other intervals, all of which finish later. Therefore, the $$k$$-th greatest finish time of all machines is at least $$f_i$$. $$\square$$

Since all of the machines in the greedy solution finish just as early as their counterparts in any other solution, we conclude that the greedy algorithm found an optimal solution.

> **Corollary.** The greedy-by-decreasing-finish algorithm finds an interval scheduling with minimum area.

In fact, we can significantly generalize the result:

> **Corollary.** Let $$c: \mathbb{R} \to \mathbb{R}$$ be any nondecreasing function, and let the cost of an interval scheduling be $$\sum_{j} c(f_j)$$, where $$f_j$$ is the maximum finish time on machine $$j$$. Then the greedy-by-decreasing finish algorithm finds an interval scheduling with minimum cost.

That was too easy, so I'm not getting a paper out of this little adventure. Still, it's fun to think about the wider consequences; if I were trying to pack spherical shells into a box, rather than rings onto a tray, I could still use the same algorithm to get an optimal solution, and it would still run in the same amount of time! Actually, this algorithm works for packing shells in *any* dimension $$d$$. I wonder what other problems this simple greedy algorithm might help us solve.

**Question.** Characterize the family of cost functions for which greedy-by-decreasing-finish finds an optimal scheduling.