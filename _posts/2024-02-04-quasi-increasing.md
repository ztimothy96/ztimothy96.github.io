---
layout: post
title: "Quasi-increasing sequences"
subtitle: "A cute analysis problem"
thumbnail-img: /assets/img/rudin-converge.png
share-img: /assets/img/rudin-converge.png
tags: [math, analysis]
---

I recently stumbled across a cute little real analysis problem about generalizing the well-known monotone convergence theorem.

> **Theorem (Monotone convergence).** Suppose that $$(p_n)$$ is a bounded, monotonically increasing sequence. Then $$(p_n)$$ converges.

>**Proof.**
Look at $$\sup (p_n)$$ and notice that it's the desired limit. The sequence gets closer to every upper bound because it's increasing, and we can't get any closer because we picked the lowest upper bound. $$\square$$

We might wonder whether we relax the monotone condition. What if the sequence isn't strictly increasing, but only "quasi-increasing"?

> **Definition.** A sequence is quasi-increasing if, for every $$\epsilon > 0$$, there exists $$N$$ such that whenever $$n > m > N$$, then $$p_n > p_m - \epsilon$$.

We might think of the definition as saying that a quasi-increasing sequence displays increasing behavior "in the limit". It can decrease sometimes, but not very much. Eventually, the rest of the sequence should decrease as little as we want.

**Example**. 
- Every increasing sequence is also quasi-increasing.
- The sequence $$p_n = 1/n$$ is quasi-increasing. It's also monotonically decreasing! But it eventually decreases by so little that it hits a limit from below.

**Question.** Can we extend the monotone convergence theorem to also work for quasi-monotonic sequences?

To get a feel, I scribbled squiggles around on a piece of paper for a bit, trying to produce a sequence that was bounded and quasi-increasing, but not convergent. I got the feeling that this shouldn't be possible, for much the same reason that monotone convergence works: you eventually sort of run out of room to decrease, so you can only go up until you hit an upper bound. But the limit of the sequence isn't its supremum anymore, because the beginning of the sequence can decrease and increase above the limit however much it likes. Remember, the sequence is only increasing "in the limit." So perhaps its limit isn't the supremum, but rather the "limit supremum."

> **Theorem (Quasi-monotone convergence).** Suppose that $$(p_n)$$ is a bounded, quasi-increasing sequence. Then $$(p_n)$$ converges.

>**Proof.**
As hinted at, we want to show that $$s = \lim \sup (p_n)$$ is the desired limit. (Note that $$s$$ is finite because the sequence is bounded.) Let $$s_n = \sup \{p_k: k > n\}$$ so that $$s = \lim s_n$$.  We know that eventually $$s_n$$ and $$s$$ will get $$\epsilon$$-close for $$n > N$$. Fixing $$N$$, we know that $$p_n$$ and $$s_N$$ will at some point get $$\epsilon$$-close (because otherwise $$s_N$$ wouldn't be the least upper bound anymore). Since the sequence is quasi-increasing, the rest of it is $$2\epsilon$$-close to $$s_N$$, and therefore $$3\epsilon$$-close to $$s$$. The end! $$\square$$

That took me about five minutes to figure out. I might not have done any math research in a long time, but at least I haven't forgotten the basics...