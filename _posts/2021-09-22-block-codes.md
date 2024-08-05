---
layout: post
title: "Block codes"
subtitle: "Algebraic coding theory, part 1"
thumbnail-img: /assets/img/message.png
share-img: /assets/img/message.png
tags: [math, coding theory, algebra]
---

This is part of a series in which I work through Prof. Mary Wootters' Youtube [lectures](https://www.youtube.com/watch?v=vfjN7MmSB6g&list=PLkvhuSoxwjI_UudECvFYArvG0cLbFlzSr) on algebraic coding theory. Last time I skimmed through the entire series and gave a bird's-eye overview of the topic. This time, we'll introduce what block codes are, what makes one good, and why it's impossible to construct a perfectly good block code.

## What are block codes?
To refresh your memory from last time, here's some motivation for studying error correcting codes. Alice wants to send a message to Bob, but the message must travel across a channel which can corrupt her message slightly. She wants to encode her message by adding some extra information so that Bob can recover her original message.

In more detail, Alice and Bob communicate using an alphabet $$\Sigma$$ with $$q$$ letters. Alice sends messages $$x$$ of length $$k$$. Instead of sending her message directly, she first encodes it as a codeword $$E(x) = c$$ of length $$n \geq k$$. An adverary Eve intercepts the message and changes a few of the letters, passing a corrupted codeword $$c'$$ to Bob. Finally, Bob decodes it to get a message $$D(c') = x'$$. We hope that he can decode the message correctly, so that $$x = x'$$.

To make this story precise, we make a few definitions.

> **Definition.** A code is a set of codewords $$ C \subseteq \Sigma^n $$.
> An encoding map is a bijective map $$E: \Sigma^k \to C$$.
> A decoding map is a map $$D: \Sigma^n \to \Sigma^k$$.

If two codewords are too similar to each other, then Eve can easily corrupt one codeword into another, and it is impossible to tell which was the intended message. Hence, we want to find codes where any pair of codewords are quite different. This motivates a notion of distance between words.

> **Definition.** The Hamming distance between two words is the number of letters at which they differ. We write the distance between $$x$$ and $$y$$ as $$\Delta(x, y)$$.

> **Definition.** The distance of a code $$C$$ is the minimum distance between any two words in the code.

In summary, we are going to look at block codes, with the following parameters.

> **Definition.** A $$(n, k, d)_q$$ code is a code of block length $$n$$, message length $$k$$, and distance $$d$$ over an alphabet with $$q$$ letters.

## What's a good code?
There are three main things we want from block codes: distance, rate, and efficiency. We will first think about the relationship between distance and rate.

As mentioned previously, we want codes with high distance, because codes with more distance can correct more errors. Let's prove this. Since there are distances involved, we can think about the problem geometrically.

> **Definition.** The ball around $$x$$ of radius $$r$$ is denoted $$B(x, r)$$.

As promised, codes with large distances are robust.

> **Theorem.** A code with distance $$d$$ can correct $$(d-1)/2$$ errors.

>**Proof.**
Draw a ball of radius $$(d-1)/2$$ around each codeword. Since $$d$$ is the distance of the code, all these balls are disjoint. A corrupted word $$c'$$ must fall within exactly one of these balls, so it must have come from the nearest codeword. $$\square$$

To get codes with large distance, Alice can simply send extremely long messages. For instance, she could repeat her message $$x$$ a million times, and the distance would increase by that much. Unfortunately, she would likely crash whatever communication channel she was using. It would be nice to find codes with high communication rate -- that is, codewords shouldn't be much longer than the original messages.

> **Definition.** The rate of a $$(n, k, d)$$ code is $$R = k / n$$.

In an ideal world, Alice would be able to find codes that have both good distance and rate. Unfortunately, this is impossible, for a simple reason: as we increase the distance between codewords, we can fit fewer codewords into the code! Hence, we can communicate fewer possible messages. This leads to a fundamental question:

> **Question.** Which distance-rate tradeoffs are even possible?

This question is actually open for binary alphabets! For larger alphabets $$q > n$$, however, we'll see how to construct optimal codes. In the meantime, mathematicians have come up with some impossibility results that give us a partial idea about what codes we can hope to find. We'll finish this post with one such result, based on a simple packing argument. First, a definition that will help us pack balls in message space.

> **Definition.** Given an alphabet $$\Sigma$$ of $$q$$ letters, the volume of a ball of radius $$r$$ in $$\Sigma^n$$ is the number of messages that lie within the ball. We denote it by $$\text{Vol}_q(r, n)$$.

Now here is the promised impossibility result.

> **Theorem (Hamming bound).** For a $$(n, k, d)_q$$ code $$C$$, the rate is $$R \leq 1 - \frac{\log_q \text{Vol}_q((d-1)/2, n)}{n}$$.

>**Proof.** Draw Hamming balls of radius $$(d-1)/2$$ in $$\Sigma^n$$ around each codeword of $$C$$. Since all the codewords are distance $$d$$ away from each other, the balls are disjoint. Now there are $$q^k$$ codewords, so $$q^k \cdot \text{Vol}_q ((d-1)/2, n) <  q^n$$, the number of all possible strings of length $$n$$. The result follows. $$\square$$

By counting the number of words at each distance $$k$$ from the center of a ball, we can find that $$\text{Vol}_q(r, n) = \sum_{k = 0}^r \binom{n}{k} (q-1)^k$$. This formula isn't particularly easy to understand, so we'll figure out how to make sense of it later.
