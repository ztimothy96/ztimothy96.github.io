---
layout: post
title: "Sequences and series"
subtitle: "Reading Baby Rudin, Chapter 3"
thumbnail-img: /assets/img/rudin-nested-sets.png
share-img: /assets/img/rudin-nested-sets.png
tags: [math, analysis]
---

Someone requested that I tutor them for a real analysis class, so I decided to refresh my memory by reading [Baby Rudin](https://en.wikipedia.org/wiki/Principles_of_Mathematical_Analysis). I felt surprised by how easily the concepts sprang back into my mind; maybe the analytical mode of thought has become second nature to me by now...

Today I'm covering sequences and series as presented in the textbook. Rudin is famous for his lean and clean exposition, but he doesn't supply much intuition. Here I'll try to avoid parroting formal proofs, and instead explain concepts as I might to a 5-year-old. (Okay, maybe the child would have to be a little older than 5, but you get the point.)

## Motivation for real analysis
Real analysis is about understanding what makes calculus work, and then generalizing those methods to increase their problem-solving power. You might remember from an introductory calculus class that we can solve lots of physics problems by finding things like derivatives and integrals, which capture the "rate of change" of physical quantities across time. These, in turn, are defined in terms of *limits* of functions. For instance, we find the integral by approximating the area under a curve with rectangles, then taking a limit as we slice the rectangles thinner and thinner. But just waving our hands in the air and proclaiming, "I'm sure that the rectangular area will eventually converge to the true area as long as we slice things thin enough!" might not sound very convincing. This kind of laziness often results in [pathological](https://en.wikipedia.org/wiki/Staircase_paradox) mistakes. To ensure we're not lying to everybody, we should carefully construct a theory that explains why derivatives and integrals make sense.

To begin, we should explain what limits even are; what does it mean for a sequence of points to get "infinitely close" to a limit point? Actually, I should first define distances, but basic topology is beyond the scope of this post. Instead, I'll dive straight into sequences and series. Let's assume for the moment that we know all about things like metric spaces and compactness, and spend some time figuring out whether points get close to each other.

## Sequences and convergence

>**Definition.** Let $$(p_n)_{n \in \mathbb{N}}$$ be a sequence of points in metric space $$(X, d)$$. We say that $$(p_n)$$ *converges* to point $$p$$ if for any $$\epsilon > 0$$, there exists $$N$$ such that $$d(p_n, p) < \epsilon$$ whenever $$n \geq N$$. We can write $$\lim_{n \to \infty} p_n = p$$.
>
> If a sequence does not converge, we say that it *diverges.*

That's a mouthful, so let's parse this definition a bit. Intuitively, a sequence $$(p_n)$$ converges to $$p$$ if its points all eventually get close to $$p$$. How close? Arbitrarily close; let's pick any tiny value $$\epsilon$$ that we want. If you give me $$\epsilon$$, I should be able to find for you some possibly very large $$N$$, so that after the first $$N$$ steps in the sequence, all the rest of the sequence stays within a ball of distance $$\epsilon$$ away from $$p$$.

![converge](/assets/img/rudin-converge.png){: .mx-auto.d-block :}

**Example.** 
- The harmonic series $$1, 1/2, 1/3, 1/4...$$ converges to $$0$$. For any tiny number $$\epsilon = 1 / N$$, we know that after the first $$N$$ values, the remaining numbers will all stay within distance $$1/N$$ of $$0.$$
- The sequence $$1, 1, 1, 1...$$ converges. Obviously.
- The sequence $$1, 2, 3, 4...$$ diverges, and it's unbounded.
- The sequence $$1, -1, 1, -1...$$ diverges, and it's bounded.

To test our understanding, let's try to prove some basic facts about sequences.

> **Theorem.** Let $$(p_n)$$ be a sequence in a metric space $$X$$.
- (a) $$(p^n)$$ converges to $$p \in X$$ iff every neighborhood of $$p$$ contains $$p_n$$ for all but finitely many $$n$$. 
- (b) If $$p \in X, p' \in X$$, and $$(p_n)$$ converges to both $$p$$ and $$p'$$, then $$p = p'$$.
- (c) If $$(p_n)$$ converges, then $$(p_n)$$ is bounded.
- (d) If $$E \subseteq X$$ and $$p$$ is a limit point of $$E$$, then there is a sequence $$p_n$$ such that $$p = \lim p_n$$.

>**Proof.**
I'm not going to write things up in mathematical lingo; that's what Baby Rudin is for. Instead, I'll focus on the intuitive ideas.
>
> (a) Should sound like an obvious restatement of the definition of convergence. Take the neighborhoods to be the $$\epsilon$$-balls, and the all-but-finitely many points inside them to be the tail end of the sequence $$(p_n): n \geq N$$.
>
> (b) If $$p_n$$ converges to both points $$p$$ and $$p'$$ at once, then it must eventually get $$\epsilon$$-close to both points. Then $$p$$ and $$p'$$ are $$2\epsilon$$-close to each other, so they're the same point.
>
> (c) Say $$(p_n)$$ converges; by definition its points eventually all fit within an $$\epsilon$$-ball of the limit $$p$$. Then we can cover the ball and the finite points outside it with a bounded box.
>
> (d) Since $$p$$ is a limit point of $$E$$, for each $$\epsilon$$ there is a point of $$E$$ that's $$\epsilon$$-close to $$p$$. Keep picking those points while making $$\epsilon$$ smaller and smaller... $$\square$$

Rudin then goes on to prove a bunch of very obvious things like "we can add limits together." The ideas are very similar, so I'm going to skip this and move on to subsequences.

>**Definition.** Let $$(p_n)_{n \in \mathbb{N}}$$ be a sequence. Picking $$n_1 < n_2 < n_3 < ...$$, the sequence $$(p_{n_i})$$ is a *subsequence* of $$(p_n)$$.

A subsequence is exactly what you'd expect: a sequence we get from an existing sequence by picking a subset of points from it in the order they arrive. Sometimes, even if a sequence diverges, we can still pick out a convergent subsequence.

> **Theorem.** 
- (a) If $$(p_n)$$ is a sequence in a compact metric space $$X$$, then some subsequence of $$(p_n)$$ converges to a point of $$X$$.
- (b) Every bounded sequence in $$\mathbb{R}^k$$ contains a convergent subsequence.

> **Proof.** It's easy enough to see that (b) follows from (a); given a bounded sequence in Euclidean space, we can wrap it in a closed box. By the Heine–Borel theorem, this closed and bounded box is compact, so $$(p_n)$$ converges in it.
>
> To see (a), suppose for contradiction that all subsequences of $$(p_n)$$ diverge. If we pick any subsequence, and any $$p \in X$$, there is always eventually a point of the subsequence which wanders $$\epsilon$$-far away from $$p$$. This is only possible if $$(p_n)$$ eventually entirely avoids the $$\epsilon$$-ball. But $$X$$ is compact, so we can cover the entire space with finitely many $$\epsilon$$-balls. There's nowhere left for our sequence to run away. $$\square$$

So far, it might seem difficult to decide whether a sequence converges or not. To prove convergence, we'd have to guess what the limit should be, and then check that everything gets close to it... but guessing is hard! We'd like a more *indirect* method of proving that a limit exists, without needing to say where it is. (How clever.) That's why we might want to think about Cauchy sequences.

>**Definition.** A sequence $$p_n$$ in metric space $$X$$ is a *Cauchy sequence* if for every $$\epsilon > 0$$ there is $$N$$ such that $$d(p_n, p_m) < \epsilon$$ if $$n, m \geq N$$.

This definition looks rather similar to that of a limit. It differs because the sequence eventually gets closer to known points inside itself, rather than an unknown limit. As hinted at: for most applications we care about, Cauchy sequences converge. To prove this, we first need a helpful fact about nested compact sets.

> **Lemma.** If $$K$$ is a sequences of compact sets in $$X$$ such that $$K_n \supseteq K_{n+1}$$ and $$\lim_{n \to \infty} \text{diam} \, K_n = 0$$, then $$\bigcap K_n$$ consists of exactly one point.

![nested-sets](/assets/img/rudin-nested-sets.png){: .mx-auto.d-block :}

> **Proof**. The intersection must contain at most one point because the diameters go to zero. But it's also nonempty because the intersection of [nested compact sets](https://proofwiki.org/wiki/Intersection_of_Nested_Closed_Subsets_of_Compact_Space_is_Non-Empty) is nonempty. $$\square$$

Maybe I should have covered the topology chapter before writing this up... I really needed to talk about the importance of compactness.

>**Theorem.**
- (a) In any metric space $$X$$, every convergent sequence is Cauchy.
- (b) If $$X$$ is a compact metric space and if $$(p_n)$$ is Cauchy in $$X$$, then $$(p_n)$$ converges to some point of $$X$$.
- (c) In $$\mathbb{R}^k$$, every Cauchy sequence converges.

>**Proof**.
Part (a) should be obvious; if points in a sequence eventually get $$\epsilon$$-close to the limit, then they must get $$2\epsilon$$-close to each other. Meanwhile, (c) follows immediately from (b) because we can cover the Cauchy sequence in a closed box as before.
>
> It remains to prove (b). To see this, let $$E_n$$ be the closure of $$(p_k: k \geq n)$$. Then $$(E_n)$$ is a sequence of closed sets nested inside each other, $$E_n \supseteq E_{n+1}$$. Since they live in a compact set $$X$$, they are all compact. Since $$(p_n)$$ is Cauchy, the diameters of $$(E_n)$$ converge to $$0$$. Now we can apply the lemma and conclude that their intersection is a single point $$p$$. It should be apparent that $$p$$ is the desired limit point, because all the remaining points in the Cauchy sequence ($$E_n$$) eventually zoom in closer and closer around it. $$\square$$

>**Definition.** A metric space where every Cauchy sequence converges is called *complete*.

Pictorially, we can visualize a Cauchy sequence as drawing closer and closer toward an area with diameter $$0$$, which sounds suspiciously like a limit point. Such a sequence could only diverge if the desired point is missing from the metric space. For instance, the rational numbers $$\mathbb{Q}$$ are *not* complete, because the Cauchy sequence $$3, 3.1, 3.14, 3.141... $$ does not converge to $$\pi$$; there is a "hole" where the limit should be. In contrast, the real numbers $$\mathbb{R}$$ complete the rationals, filling in all the missing holes.

Okay, maybe that's enough math review for one day. The moral of this story is that we like to do calculus on $$\mathbb{R}^k$$ because all the subspaces we care about are compact, and nice things happen on compact sets. In particular,
- On a compact set, every sequence contains a convergent subsequence.
- On a compact set, every Cauchy sequence converges.

I suddenly want to write another post explaining compactness, but there are other things to do...