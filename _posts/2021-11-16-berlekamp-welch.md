---
layout: post
title: "The Berlekamp-Welch algorithm"
subtitle: "Algebraic coding theory, part 6"
thumbnail-img: /assets/img/message.png
share-img: /assets/img/message.png
tags: [math, coding theory, algebra]
---
This is part of a series in which I work through Prof. Mary Wootters' Youtube [lectures](https://www.youtube.com/watch?v=vfjN7MmSB6g&list=PLkvhuSoxwjI_UudECvFYArvG0cLbFlzSr) on algebraic coding theory. We previously saw Reed-Solomon (RS) codes have optimal rate, and in this post, we'll see how to decode errors with them.

## The decoding problem
Recall that in an $$(n, k)$$-RS code, the messages are polynomials of degree $$< k$$, and the codewords are evaluations of message polynomials at $$n$$ points $$(\alpha_i)$$. Moreover, the distance of the code is $$d = n - k + 1$$, so we can hope to correct up to $$e = (n-k)/2$$ errors.

Let's rephrase the standard decoding problem for an RS code. We're given a corrupted codeword, a set of points $$w = (w_1, ..., w_n) \in \mathbb{F}_q^n$$, This word is close to the original codeword; there should be some $$f \in F_q[X]$$ of degree $$< k$$ such that there are at most $$e$$ errors $$E = \{i : f(\alpha_i) \neq w_i\}$$. We want to recover the original message, which means finding a polynomial $$f$$ if one exists, and reporting that there are too many errors otherwise.

The key idea: consider the error locator polynomial $$E(X) = \prod_{i \in E} (x - \alpha_i)$$. Its roots are exactly the $$\alpha_i$$ which correspond to errors in our message. Observe that $$w_i E(\alpha_i) = f(\alpha_i) E(\alpha_i)$$, because they take the same values when $$w_i$$ is not corrupted, and they are both $$0$$ otherwise. Let $$f \cdot E = Q$$, so that $$w_i E(\alpha_i) = Q(\alpha_i)$$. Now if we knew $$Q$$ and $$E$$, we could recover the original codeword, $$Q / E = f$$. Unfortunately, we don't know either of them! But we can get around this problem in a magical way: we're going to simply guess $$Q$$ and $$E$$, and then we'll compute $$Q / E = f$$ and hope that's the right answer.

>**Algorithm** (Berlekamp-Welch).
>
>1. Find:
>  - a monic polynomial $$E(X)$$ of degree $$e$$, and
>  - a polynomial $$Q(X)$$ of degree $$\leq e + k - 1$$,
>  - satisfying $$w_i E(\alpha_i) = Q(\alpha_i)$$ for all $$i$$.
>  If we can't find these objects, then return failure.

This isn't very satisfying so far. How can we find $$E(X)$$ and $$Q(X)$$ satisfying these conditions? And even if we could, why can't $$Q(X)/E(X)$$ decode to an incorrect codeword, $$\tilde{f} \neq f$$?

## Why is the algorithm correct?
Let's see why this algorithm gives us the right answer before we figure out how to implement it. First, if there really is a nearby message $$f$$, then there are some polynomials $$Q(X), E(X)$$ that will help us find $$f$$.

>**Proposition.** If there is some polynomial $$f$$ such that $$\Delta(f, w) \leq e$$, then there exist some $$Q(X), E(X)$$ satisfying the algorithm's conditions such that $$Q(X) / E(X) = f(X)$$.

>**Proof.** This is the same idea we already saw. Take $$E(X) = \prod_{i \in E} (x - \alpha_i) x^{e - \Delta(f, w)}$$, modifying the error locator polynomial to have degree exactly $$e$$. Then, simply define $$Q(X) = E(X) f(X)$$. $$\square$$

The more interesting insight is that, no matter which $$Q(X)$$ and $$E(X)$$ we pick, we can always recover the message correctly!

>**Proposition.** Suppose that $$E_1(X), Q_1(X)$$ and $$E_2(X), Q_2(X)$$ are pairs of polynomials satisfying the algorithm's conditions. Then $$Q_1(X)/E_1(X) = Q_2(X)/E_2(X)$$.

>**Proof.** We wish to prove that $$R(X) = Q_1(X) E_2(X) - Q_2 (X) E_1(X) = 0$$. Well, there's only really one trick in the book. By definition, $$Q(\alpha_i) = w_i E(\alpha_i)$$, so for each $$\alpha_i$$, $$R(\alpha_i) = w_i E_1(\alpha_i) E_2(\alpha_i) - w_i E_2(\alpha_i) E_1(\alpha_i) = 0$$, and $$R$$ has at least $$n$$ roots. However, $$E(X)$$ is degree $$e$$ and $$Q(X)$$ has degree $$ < e + k$$, so $$R(X)$$ has degree $$< e + e + k = n$$. Nonzero low-degree polynomials can't have too many roots, so we conclude that $$R(X) = 0$$. $$\square$$.

## How to find the error locator
Now we need to find $$E(X)$$ and $$Q(X)$$ efficiently. Fortunately, we can do this by polynomial interpolation. The variables of the system are coefficients of $$E(X)$$ and $$Q(X)$$, and the constraints are simply the defining condition, $$Q(\alpha_i) = w_i E(\alpha_i)$$ for each $$i$$. There are $$\leq e + (e + k - 1) = n-1$$ variables and $$n$$ constraints. Doing this naively via Gaussian elimination, we can recover the message $$f$$ in $$O(n^3)$$ time.

There are faster algorithms, such as the Berlekamp-Massey algorithm, which runs in $$O(n^2)$$ time.

A few thoughts: I personally don't see how I could have come up with this algorithm. The idea of considering the error locator and guessing $$E(X), Q(X)$$ seems like it should have made the decoding problem more complicated rather than simpler. Once I saw the basic idea, then it was easy enough to prove that it worked, but coming up with the idea itself seems like a magic trick, pulling a neat factorization out of thin air!

I suppose that the real insight comes from introducing the error locator polynomial. This polynomial somehow bridges the gap between message symbols $$f(\alpha_i)$$ and the corrupted symbols $$w_i$$. I'm still a bit mystified as to why all the extra solutions $$E(X), Q(X)$$ still recover the same message, and I wonder how the solutions relate to the particular choice of errors. That's a rather vague question, to be sure.
