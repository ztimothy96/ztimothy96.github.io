---
layout: post
title: "Onion rings and interval scheduling"
thumbnail-img: /assets/img/algorithms.png
share-img: /assets/img/algorithms.png
tags: [math, algorithms]
---

I've wanted to keep doing mathematical research as a hobby, but it's difficult to come up with interesting problems without collaborators to bounce ideas off. Nonetheless, algorithms sometimes present themselves in everyday life, such as when I air-fried onion rings with my family over the Christmas break. There were too many onions to fit into the tray, so I packed in as many as I could, fry them for a few minutes, and repeat with the remaining rings. To reduce cooking time, I tried to pack the onion rings into fewer trays, and as a computer scientist I couldn't resist a lovely packing problem.  

Initially, I noticed that we might as well fit the smaller rings into the larger ones, or else we'd waste the space in the interior of the larger ring. Thus, I first arranged the onion rings into concentric circular stacks, and then tried to fit them onto the tray with a circle-packing heuristic. As I worked, I began mulling over the following question: given a collection of $$n$$ onion rings, how can we pack smaller rings inside the larger ones, minimizing the total number of stacks?

It soon dawned on me that this problem felt eerily similar to [interval scheduling](https://en.wikipedia.org/wiki/Interval_scheduling). After all, we can model each $$i$$-th onion ring as an interval with start and finish $$[s_i, f_i], s_i < f_i$$, corresponding to its inner and outer radii respectively. We can fit two onion rings into a concentric stack if their intervals do not overlap. The classic interval scheduling problem asks us to find a maximum subset of nonoverlapping intervals, which corresponds to creating a single stack of many non-overlapping onion rings. It's well-known that we can solve this problem in $$O(n \log n)$$ time by greedily adding the interval with the earliest available *finish*. Intuitively, this works because it leaves us the most amount of space to schedule later intervals.

![rings](/assets/img/rings.png){: .mx-auto.d-block :}
*A concentric stack of rings.*

Interval scheduling originally helped to assign many non-conflicting tasks to a single machine over time. There are many [variations](https://en.wikipedia.org/wiki/Optimal_job_scheduling) on this theme, for computer scientists have wanted to schedule tasks on machines as long as computers have existed. Most of the examples I could find in the literature start with a fixed number of machines and optimize some version of processing time, such as the maximum or average time for a machine to complete all its tasks. However, my problem features a twist: I wanted to minimize the number of stacks that would include every onion ring, which corresponds to minimizing the number of machines needed to complete the tasks!

Faced with this new problem, I began by asking whether the familiar greedy-by-finish strategy works. If it can quickly pack a maximum number of tasks onto a single machine, then maybe repeatedly applying it to the remaining tasks will pack them into as few machines as possible. Unfortunately, this approach fails, because a greedy iteration might leave behind tasks that conflict with each other. Consider the following counterexample:

![intervals](/assets/img/intervals.png){: .mx-auto.d-block :}

We have $$n = 4$$ intervals, namely $$I_1 = [1, 3], I_2 = [2, 5], I_3 = [6, 7], I_4 = [4, 8]$$.

An optimal schedule uses only two machines, assigning $$I_1, I_4$$ to machine 1 and $$I_2, I_3$$ to machine 2. However, the greedy algorithm would assign $$I_1, I_3$$ to machine 1. The remaining intervals $$I_2, I_4$$ conflict, so they must be scheduled on different machines. Thus, the greedy schedule would require three machines, which is suboptimal.

> **Question.** What is the worst-case approximation ratio of the greedy-by-finish strategy?

However, we can greedily add the intervals ordered by earliest *start* instead of finish. For each interval, we schedule it on an arbitrary available machine. If there are none available, then we have to schedule the interval on a new machine. In the above example, this algorithm would first schedule $$I_1$$ on machine 1, then $$I_2$$ on machine 2. It would then realize that $$I_4$$ conflicts with $$I_2$$, and instead schedule it on machine 1. Finally, it would assign $$I_3$$ to machine 2. Implemented with a heap, this strategy runs in $$O(n \log n)$$ time, and it always finds the minimum number of required machines!

> **Theorem.** The greedy-by-start algorithm is optimal.

> **Proof.** Consider the moment we schedule the first interval $$i$$ on the last machine $$k$$. At that moment, we must have scheduled it to machine $$k$$ because the remaining $$k-1$$ machines were already in use. Thus, it is not possible to use fewer than $$k$$ machines. $$\square$$

Well, that was too easy... I guess I didn't find a result worth publishing. Still, this exercise got me thinking about what happens if we change the objective functions of interval scheduling problems. For instance, if I want to pack the rings onto a small tray, perhaps I shouldn't care about the number of stacks, but rather their total *area*. That is, each solution consists of stacks with outer radii $$f_j$$, and I could try to minimize their area $$\sum_j f_j^2$$. Alternatively, I could consider the problem in lower dimensions (packing on a line rather than a tray) and minimize the total length of stacks, $$\sum_j f_j$$. But that's a story for a different time.

It might also be fun to read more about interval graphs and the snake-in-a-box problem... supposedly, this somehow connects to the design of error-correcting codes. In the meantime, I'll enjoy eating my onion rings.