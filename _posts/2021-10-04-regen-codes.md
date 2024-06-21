---
layout: post
title: "Regenerating codes"
subtitle: "Algebraic coding theory, part 4"
thumbnail-img: /assets/img/message.png
share-img: /assets/img/message.png
tags: [math, coding theory, algebra]
---

This is part of a series in which I work through Prof. Mary Wootters' Youtube [lectures](https://www.youtube.com/watch?v=vfjN7MmSB6g&list=PLkvhuSoxwjI_UudECvFYArvG0cLbFlzSr) on algebraic coding theory. Recently I've decided to just skip around watching whatever catches my interest. Today, we'll cover regenerating codes, which can recover missing parts of a codeword using only a little communication.

I'm also trying to cover material rapidly so the quality of writing may suffer somewhat. Maybe I can go back and revise as I understand things better.

## What are regenerating codes?

Regenerating codes are motivated by applications in distributed storage. Suppose that Bob receives a codeword from Alice and stores it on a distributed network. To ensure robustness, he stores different letters of the codeword on different computers. That way, even if a few computers catch on fire, most of the data will stay safe. However, Bob might then want to regenerate the data that was just destroyed.

To recover a single letter of the codeword, Bob could use a locally correctable code (LCC). However, this seems a little inefficient. In the LCC model, there is a constant probability of error $$p$$, and all the errors are corruptions. In contrast, Bob expects that only a constant number of his computers will catch fire, and he only needs to correct erasures (since he knows which computers have exploded). Since we expect less tricky errors, maybe we can design codes with better rate-distance tradeoff.

Unfortunately, this goal isn't strictly possible. Maximum distance separable (MDS) codes have optimal distance meeting the singleton bound, so they can correct any $$k$$ erasures. This property is great for error correction, but terrible for locality, because looking at only $$k-1$$ symbols does't tell us about the rest of the codeword. So, we need to find a loophole. Instead of looking at only a few letters of the word, let's instead limit our bandwidth. Bob can receive information from all of his computers, but he would like to download only a few bits from each. As usual in distributed models, each computer can do its own computation to decide what to send to Bob.

## RS codes, again
It turns out that Reed-Solomon (RS) codes are regenerating codes! To illustrate why this is true, we'll look at a nice, simple case, though the argument should work more generally. Consider the code $$RS(n, k)$$ over $$\mathbb{F}_q$$ where $$q = n = 2^t$$ and $$k = n/2$$. There is a decoding algorithm where Bob only receives one bit from each computer. If he naively decoded the entire word, he would need to look at $$n/2$$ letters, which requires at least $$(n \log n) / 2$$ bits. This means our new scheme communicates $$(\log n)/ 2$$ times more efficiently.

Let's try to discover this algorithm by thinking about what we want. Recall that in RS codes, the messages are polynomials of degree $$< k$$, and codewords are their evaluations at points $$\alpha$$ over a field $$\mathbb{F}_q$$. Given a message $$f$$, each computer stores a letter $$f(\alpha)$$. Let's say that the computer storing $$f(0)$$ goes missing. We want to recover $$f(0) \in \mathbb{F} _{2^t}$$, which we can think as a binary vector of length $$t$$.

How do we recover coordinates of a vector? Usually, we would take the inner product with a basis. That is, we'd like to find out $$ \langle f(0), f(\alpha) \rangle$$ for several vectors $$\alpha \in \mathbb{F}_{2^t}$$. However, $$\mathbb{F} _{2^t}$$ is a finite field, not a Hilbert space, so we haven't defined an inner product on it. Fortunately, there's a mathematical object that acts similarly to an inner product: the field trace.

## Tracing back

For simplicity, we're going to consider traces on fields $$\mathbb{F}_{2^t}$$. Although we can define traces for maps from any vector space to itself, the binary case should be enough to communicate the main ideas. Basically, each computer should use its stored letter $$\alpha, f(\alpha)$$ and compute some bit $$\mathbb{F} _{2^t} \to \mathbb{F}_2$$ to send to Bob. Let's say we want to do this in a linear way, like an inner product. We want to think about field traces, because *any* linear function of this sort can be written as a field trace. (This is analogous to the fact that any linear function on a vector space can be written as an inner product with a dual vector.)

>**Definition.** The field trace is a function $$\text{tr}: \mathbb{F}_{2^t} \to \mathbb{F}_2$$ by $$\text{tr} (X) = X + X^2 + ... + X^{2^{t-1}}$$.

>**Theorem.** The trace is $$\mathbb{F}_{2^t}$$-linear. All linear functions $$f: \mathbb{F} _{2^t} \to \mathbb{F}_2$$ can be written as a shifted trace $$f(X) = \text{tr}(\zeta X)$$ for some $$\zeta \in \mathbb{F} _{2^t}$$.

We'll need one more fact that helps us relate the letters stored across the different computers.

>**Proposition.** If polynomials $$f, g \in \mathbb{F}_n[X]$$ both have degree less than $$n/2$$, then $$\sum _{\alpha \in \mathbb{F}_n} f(\alpha) \cdot g(\alpha) = 0$$.

With that in mind, here's how we can regenerate letters with an RS code.

>**Theorem.** There is an algorithm that corrects one erasure of an $$RS(n, n/2)$$ code using one bit from each letter.

>**Proof.** Define the polynomial $$g_\zeta(X) = \text{tr}(\zeta X) / X$$. Suppose $$f$$ is a valid message with $$\text{deg}(f) \leq n / 2$$. We can relate the missing letter $$f(0)$$ with all the other letters of the codeword, $$f(0) g_\zeta(0) = \sum_{\alpha \neq 0} f(\alpha) g(\alpha)$$. Taking the trace of both sides, and using its linearity,
>
>$$\begin{align}
\text{tr}(f(0) \zeta) &= \text{tr} \left(\sum_{\alpha \neq 0} f(\alpha) g(\alpha) \right) \\
&= \text{tr} \left(\sum_{\alpha \neq 0} f(\alpha) \text{tr}(\zeta \alpha) / \alpha \right) \\
&= \sum_{\alpha \neq 0} \text{tr}(\zeta \alpha) \text{tr}(f(\alpha) / \alpha).
\end{align}$$
>
>Then each node containing the letter $$f(\alpha)$$ sends the bit $$\text{tr}(f(\alpha)/\alpha)$$. Since Bob already knows $$\alpha, \zeta$$, he can use all of the received bits to compute $$\text{tr}(f(0) \zeta)$$. Since he can recover this value for every $$\zeta$$, he can pick enough to recover the value of $$\alpha$$. $$\square$$

## More to explore
There's much more to think about beyond the basic idea presented here. Proceeding similarly, we can extend this idea to work with different fields $$\mathbb{F}_q$$ and with different parameters for the code, $$RS(n,k)$$. We can also generalize this scheme to correct more than one erasure. The main idea is to replace the identity $$\sum _{\alpha \in \mathbb{F}_n} f(\alpha) \cdot g(\alpha) = 0$$ with many identities, which results in a low-rank linear system. That way we only need to send a few bits from each node. For more details, check out [this](https://www.youtube.com/watch?v=LXBr0FR6n5I) talk by the same lecturer at the Shannon Channel.

Some reflections: I'm not sure I would want to actually implement this in a distributed storage system. The codes seem much more complicated than straightforward replication, so they might add to computational costs and delays on the network. Since the bandwidth saving is $$(\log n)/2$$, it doesn't seem worthwhile unless $$n$$ is extremely large and we can't afford more computing power. The Shannon Channel talk above mentioned that the bandwidth presented is optimal for small alphabet sizes and linear schemes (using trace), so improving it seems difficult. Still, the scheme presented here is quite beautiful from a purely aesthetic point of view. Besides, low-communication polynomial interpolation seems like a pretty fundamental operation, so maybe it will find other applications?

The roadblocks I mentioned lead to another question: can we do better if the decoding scheme is non-linear? What happens if we don't use field traces, and instead the nodes can use their stored letter $$f(\alpha)$$ to compute *any* function $$\mathbb{F}_{2^t} \to \mathbb{F}_2$$ whatsoever? This seems like an intriguing research question, although I'm not sure if it's still open.
