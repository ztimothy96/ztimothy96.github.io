---
layout: post
title: "List recovery"
subtitle: "Algebraic coding theory, part 7"
thumbnail-img: /assets/img/message.png
share-img: /assets/img/message.png
tags: [math, coding theory, algebra]
---
This is part of a series in which I work through Prof. Mary Wootters' Youtube [lectures](https://www.youtube.com/watch?v=vfjN7MmSB6g&list=PLkvhuSoxwjI_UudECvFYArvG0cLbFlzSr) on algebraic coding theory. This post introduces list recovery, a generalization of the list decoding problem.

## The list recovery problem

As usual, Alice sends messages to Bob, but Eve corrupts a fraction $$p$$ of the $$n$$ codeword letters. Suppose that, in addition to their other communication issues, Alice has messy handwriting. Upon receiving a word of length $$n$$, Bob can't tell what the $$i$$-th letter is supposed to be -- he can only guess that the intended letter was in some set $$S_i$$. But as long as Alice's handwriting isn't *too* messy, Bob's list of guessed letters $$\Sigma_i$$ might be rather short. Maybe there are only $$\ell$$ letters that look like a good match. With that, Bob might hope to decode the garbled codeword into a short list $$L$$ of possible messages. Then he can try to find the right message inside his list, using other contextual information.

With that story in mind, we're ready to define list recovery.

>**Definition.** A code $$C \subseteq \mathbb{\Sigma}^n$$ is $$(p, \ell, L)$$-list-recoverable if, for all sets $$S_1, ..., S_n \subseteq \Sigma$$ with each $$S_i$$ containing at most $$\ell$$ symbols, there are at most $$L$$ codewords $$c$$ that disagree with the received information at only $$pn$$ coordinates $$i: c_i \not \in S_i$$.

Here are some observations about the list recovery problem.

- It generalizes the list decoding problem. When $$\ell=1$$, Alice's handwriting is clean enough that Bob can read the letters in the corrupted message, and we get the $$(p, L)$$-list decoding problem.
- The problem is also interesting when $$p=0$$. In this case, Alice still has messy handwriting, but at least there's no adversary Eve corrupting her messages even further. We'll call this the $$(\ell, L)$$-list recovery problem.
- We'll always have $$L \geq \ell$$ (the list size increases with the amount of guesswork). To see this, take any arbitrary $$\ell$$ codewords $$C = \{c_1, ..., c_\ell\}$$, and make $$S_i$$ the collection of $$i$$-th letters from those codewords. Then $$C$$ agrees with the sets $$S_i$$ perfectly, so the list size $$L$$ must be at least $$\ell$$.

We can ask the same questions that we looked at for classical coding, and then for list recovery: what is the best list recovery capacity we can hope for? Is there a "Johnson bound" showing that reasonably good list recoverable codes exist? (Left as an exercise.)

## List recovering RS codes
Once again, we'll return to our favorite codes, the Reed-Solomon (RS) codes, ask whether they are list recoverable. Recall that in RS codes, the messages are polynomials $$f$$, and their encodings are evaluations at some points $$\alpha$$. We can rephrase the list recovery problem in the language of RS codes.

>**Problem.**
Let $$\alpha = (\alpha_1, ..., \alpha_n)$$ be evaluation points in $$\mathbb{F}_q$$. Given $$S_1, ..., S_n \subseteq \mathbb{F}_q$$, where each $$\vert S_i \vert \leq \ell$$, find all polynomials $$f \in \mathbb{F}_q[X]$$ of degree $$<k$$ such that $$f(\alpha_i) \in \S_i$$ for all but $$pn$$ of the coordinates $$i$$.

Essentially, we have a bunch of points floating around in space, and we want to find all the polynomials that fit through a lot of the points. Fortunately, we can already solve this problem using the Guruswami-Sudan algorithm, which was discovered in the context of list decoding. This immediately implies the following result. (It looks a bit complicated -- I'm just going to quote the result, point out where it comes from, and ignore the messy algebra.)

>**Theorem.** Let $$\epsilon > 0$$. The code $$\text{RS}_q(n, k)$$ is efficiently $$(p, \ell, L)$$-list-recoverable for $$p < 1 - (R\ell(1+\epsilon))^{1/2}$$, and $$L = O(1/\epsilon \cdot (\ell/R)^{1/2})$$.

>**Proof.**
For a set of guessed letters $$S_i = (y_1, y_2, ..., y_\ell)$$, create interpolation points $$(\alpha_i, y_j): j \in [\ell]$$. Repeating this for each $$i$$ yields $$M = n\ell$$ interpolation points. Apply the Guruswami-Sudan algorithm to all these points, picking $$p$$ small enough to ensure that the algorithm will run correctly. $$\square$$

Okay, that's old news, which doesn't seem terribly exciting. Like I said, I wasn't planning on learning about list decoding and recovery at first. However, this work apparently has applications in solving other algorithmic problems such as streaming, so we'll see some of those sweet connections next time.
