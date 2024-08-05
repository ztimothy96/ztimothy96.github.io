---
layout: post
title: "Linear codes"
subtitle: "Algebraic coding theory, part 2"
thumbnail-img: /assets/img/message.png
share-img: /assets/img/message.png
tags: [math, coding theory, algebra]
---

This is part of a series in which I work through Prof. Mary Wootters' Youtube [lectures](https://www.youtube.com/watch?v=vfjN7MmSB6g&list=PLkvhuSoxwjI_UudECvFYArvG0cLbFlzSr) on algebraic coding theory. Last time we introduced block codes and the conflict between distance and rate. This time, we'll introduce linear codes, which are codes that we design using linear algebra.

Recall that a code $$C$$ is a set of codewords in $$\Sigma^n$$. When Alice wants to send a message to Bob, she takes her encoding map $$E: \Sigma^k \to \Sigma^n$$ to get a codeword $$c = E(x)$$. Since mathematicians understand linear algebra very well, we might wonder how well a code can perform when its encoding map is linear. As we'll see, linear codes are convenient because we get efficient algorithms for several tasks:
1. encoding messages,
2. detecting $$d-1$$ errors, and
3. correcting $$d-1$$ erasures.

## Encoding messages
We want a code that is easy to encode messages with. Since linear maps are easy to compute, that motivates us to study linear codes!

> **Definition.** A code $$C$$ is linear if it is the column space of a linear map $$G: \Sigma^k \to \Sigma^n$$. That is, $$C = \{Gx: x \in \Sigma^k\}$$. We say $$G$$ is a generator matrix for $$C$$.

(Note that for this to work, $$\Sigma$$ should be a field, which is basically an algebraic structure that we can build vector spaces and do linear algebra on. Since $$\Sigma$$ contains only $$q$$ elements, it is called a finite field. There is a unique finite field for each prime power $$q$$.)

To find a generator matrix $$G$$, we can pick the column vectors to be any basis for $$C$$. So, generator matrices are not unique. Moreover, some codes are very similar to each other, in that you can translate codewords from one code to the other by shuffling around the order of the letters. Since these codes have exactly the same performance parameters $$(n, k, d)$$, we consider these codes equivalent.

> **Definition.** Two codes $$C, C'$$ are equivalent if they differ by a permutation, that is, there is some permutation $$\sigma$$ on the letters such that $$\sigma: C \to C'$$ is a bijection.

It turns out that, if we shuffle around the letters enough, we can always find a particularly nice generator matrix:

> **Theorem.** Up to permutation, any linear code has a generator matrix $$\begin{pmatrix} I \\ A \end{pmatrix}$$. We say that this generating matrix is canonical.

A canonical generating matrix sends $$Gx = x \bigoplus Ax$$. This has a clean interpretation: the first $$k$$ letters contain the original message, and the remaining $$n - k$$ letters help to correct errors.

## Detecting and correcting errors
Now that we've defined linear codes, we should figure out how to decode with them. We can detect up to $$d-1$$ errors by checking whether or not our received string is a code word. To do that, we need to check whether a vector $$\tilde{c}$$ lies in the column space of a generator matrix $$G$$. Fortunately, this is easy if we use another idea from linear algebra: orthogonal subspaces.

> **Definition.** Let $$C$$ be a linear code. Its dual code is its orthogonal subspace, $$C^{\perp} = \{ v: \langle v, c \rangle = 0 \text{ for all } c \in C\}$$.

By taking a basis for the dual code, we can get a generator matrix for it, called $$H^T$$. Since $$H^T$$ spans $$C^\perp$$, the kernel of $$H$$ is exactly $$C$$. So, we can figure out whether a vector $$\tilde{c}$$ lies in $$C$$ by simply checking whether $$H\tilde{c} = 0$$. We can think of each row of this matrix as a constraint: if $$c$$ is a codeword, then a certain linear combination of its letters add up to zero. That is, each row performs a parity check.

> **Definition.** Let $$C$$ be a linear code. A matrix $$H$$ is a parity check matrix for the code if $$C = \ker(H)$$.

We can also use parity check matrices to correct $$d-1$$ erasures. Given a vector $$\tilde{c}$$ with at most $$d-1$$ letters erasured, we can recover the original codeword by solving a linear system so that $$H \tilde{c} = 0$$. As long as there are only a few erasures, there will be a unique solution.

So far we've seen that generator matrices encode messages, and parity check matrices detect and decode errors. Based on the discussion so far, hopefully the following seems intuitive:

> **Proposition.** Let $$C$$ be a linear $$(n, k, d)$$-code with generator $$G$$ and parity check $$H$$. Then,
1. $$H \cdot G = 0$$.
2. The dual code $$C^{\perp}$$ is a code with block length $$n$$ and message length $$n-k$$.
3. $$C^{\perp}$$ has generator $$H^T$$ and parity check $$G^T$$.
4. The distance $$d$$ is the minimum number of linearly dependent columns in $$H$$.

> **Proof.** Properties 1-3 are direct consequences of how we came up with generators, parity checks, and dual codes. Property 4 holds for the same reason that we can correct only up to $$d-1$$ erasures: there can be two codewords satisfying a linear system with $$d$$ unknowns. $$\square$$

We would also like to correct up to $$(d-1)/2$$ substitution errors. Unfortunately, finding an algorithm to correct errors for an arbitrary linear code is NP-hard. We need to come up with specific linear codes with extra structure that helps us decode them -- more on this later.

> **Question.** Is there a relationship between the distances of $$C$$ and $$C^{\perp}$$?

## GV Bound
The Gilbert-Varshamov (GV) bound is a possibility result, which says that there exist linear codes with pretty good distance-rate tradeoff.

> **Theorem (GV bound).** For any prime power $$q$$, there exists a linear $$(n, k, d)_q$$ code with rate $$R \geq 1 - \frac{\log_q \text{Vol}(d-1, n) - 1  } {n}.$$

> **Proof.** Picking a random code will do! Alternatively, there is a deterministic construction: construct a basis for the code by throwing in linearly independent vectors until you run out of vectors.

As you'd expect, the distance-rate tradeoff here is slightly worse than the bound given by impossibility results like the Hamming bound. It's still pretty good, losing only a factor of two in the volume. Unfortunately, neither of these constructions is explicit. While it's easy to produce a random code, it's very difficult to check whether or not the result has a good rate. On the other hand, the deterministic construction is too slow, as it requires checking across the $$q^n$$ vectors to find another linearly independent one.
