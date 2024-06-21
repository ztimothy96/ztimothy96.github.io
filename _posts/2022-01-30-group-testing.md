---
layout: post
title: "Group testing"
subtitle: "Algebraic coding theory, part 9"
thumbnail-img: /assets/img/message.png
share-img: /assets/img/message.png
tags: [math, coding theory, algebra]
---
This is part of a series in which I work through Prof. Mary Wootters' Youtube [lectures](https://www.youtube.com/watch?v=vfjN7MmSB6g&list=PLkvhuSoxwjI_UudECvFYArvG0cLbFlzSr) on algebraic coding theory. This post is about using codes to design group tests for finding defective items.

## The group testing problem
Originally the group testing problem was studied in the context of trying to detect disease in the US Army. Suppose that a disease has broken out, and you want to find all the people in your population who carry the virus. You have a test that can detect the disease in a blood sample. The obvious thing to do is to simply test everybody in your population. Unfortunately, while collecting blood samples is relatively cheap, performing the test on each sample is expensive. Moreover, the disease outbreak is still in its early stages, so you expect only a small number of positive cases. How can you detect all the positive cases using the minimum number of tests?

One idea that comes to mind involves *group testing*. Instead of testing a single person with each blood sample, we can take a bunch of blood samples from a group of people and pool them together. Then a positive test would reveal that at least one person in the group has the disease, while a negative test would show that nobody does.

Let's translate this scenario into a math problem. Suppose that we have a population of $$N$$ people, and that there are only $$t$$ people with the disease, where $$t \ll N$$. We want to find a small set of tests which will reveal which of the people carry the virus. Let's represent each test as a $$N$$-dimensional boolean row vector, where the $$i$$-th entry tells us whether person $$i$$ is pooled into the test. Then we can put each of the $$m$$ tests as a row in a $$N \times m$$ dimensional matrix, called the *pooling matrix*.

Now that we can represent our tests as a pooling matrix $$\Phi \in \mathbb{F}_2^{N \times m}$$, we need to determine which kinds of tests allow us to do group testing. Imagine receiving the test results and trying to find the $$t$$ positive cases. If a person is in a test which comes out negative, then we know that they do not carry the virus. However, if they are only in positive tests, then we can't be sure they have the virus, or whether a different positive case happened to be in their pooled samples. To be certain, there must be a test which separates them from other positive cases. No matter which people $$\Lambda$$ really have the disease, for every other person $$j$$, we should always be able to find a test $$i$$ which will give a negative result. This motivates the following definition.

>**Definition**. A $$t$$-disjunct *pooling matrix* $$\Phi$$ is one such that, for every set $$\Lambda \subseteq [N]$$, with $$\vert \Lambda \vert \leq t$$, for each $$j \in [N] \setminus \Lambda$$, there exists some test $$i$$ such that $$\Phi_{ij} = 1$$ but $$\Phi_{i\ell} = 0$$ for all $$\ell \in \Lambda$$.

Since we want to perform only a few tests, we want a $$t$$-distinct pooling matrix with only a few rows.

**Question.** What is the minimum $$m$$ for which there is a $$t$$-distinct $$N \times m$$ pooling matrix?

## The Kautz-Singleton construction

Fortunately for the US Army (and lightbulb manufacturers, and other people who have found applications for group testing), we know how to construct pooling matrices which are nearly optimal. The Kautz-Singleton (KS) construction is one method which uses ideas from coding theory.

As always, let's try to discover this idea for ourselves. What are the similarities between group testing and error correcting codes? In group testing, we're trying to construct a $$t$$-disjunct matrix, which intuitively represents tests such that separates each person's tests from everyone else's. The separation between their tests allow us to find the true positive cases. Meanwhile, in coding theory, we want to design efficient codes whose codewords are far away from each other, so that we can find and correct errors. The analogy here is clear: each person corresponds to a codeword, and separation corresponds to Hamming distance between them.

**Construction.**
With that in mind, let $$C$$ be a $$(n, k, d)$$ code over $$\mathbb{F}_q$$. Since every person corresponds to a codeword, we want $$N$$ codewords, which means we should take $$N = q^k$$. Unfortunately for us, the codewords are vectors over the $$\mathbb{F}_q$$, but a pooling design needs to be boolean, over field $$\mathbb{F}_2$$. To work around this issue, we can simply represent each of the $$q$$ letters of as a characteristic vector, where the $$i$$-th entry is $$1$$ if we are looking at the $$i$$-th letter of $$\mathbb{F}_q$$, and $$0$$ otherwise. This blows up the dimension of our codewords by a factor of $$q$$.

So the construction is easy to compute. Taking all of the codewords of $$C$$, blowing them up into binary, and putting them side-by-side yields the pooling matrix $$\Phi \in \mathbb{F}_2^{N \times nq}$$. But when is this constructed matrix $$t$$-disjunct?

**Correctness.**
As we guessed before, disjunctness comes from having a code with good distance. To make this relationship precise:

>**Theorem.** In the KS construction, if $$d > n(t-1)/t$$, then $$\Phi$$ is $$t$$-disjunct.

>**Proof**. For the KS pooling matrix to be $$t$$-disjunct, we need the following. For any codewords $$\Lambda \subseteq C$$ with $$\vert \Lambda \vert \leq t$$, and any other word $$c \in C \setminus \Lambda$$, there exists some coordinate $$i$$ where $$c$$ differs from all $$w \in \Lambda$$. (Namely, $$c_i \neq w_i$$.) After all, suppose that $$c_i = \alpha$$; then the indicator vector for letter $$\alpha$$ will be $$1$$, but it will be $$0$$ for everything in $$\Lambda$$.
>
So we need to figure out when $$c$$ must differ from a collection of other codewords $$\Lambda$$. Let's start simple: if $$\vert \Lambda \vert = 1$$, then there is only one codeword, and $$c$$ differs in at least $$d$$ coordinates, but it might match letters in $$n-d$$ coordinates. Each time we toss another codeword into $$\Lambda$$, it might match $$c$$ in another $$n-d$$ coordinates. Hence, the total number of matching coordinates is at most $$(n-d)t$$. We want to find a leftover coordinate where $$c$$ doesn't match anything; this exists when $$(n-d)t \leq n$$. Solving, $$n(t - 1)/t < d$$. $$\square$$

Since we want a really high distance code, let's plug in a Reed-Solomon code $$\text{RS}(n, k)$$, which is known to have optimal distance $$d = n - k + 1$$. We pick the smallest possible field size $$q = n$$. Solving, we find that our pooling matrix will be disjoint when $$k = q / t$$. Solving some more, we conclude that $$q = t \log_q N$$, and the number of tests required is $$m = nq = q^2 = O(t^2 \log_t^2 N)$$.

>**Theorem.** There is an efficient algorithm to find a $$t$$-disjunct, $$N \times m$$ pooling matrix where $$m = O(t^2 \log_t^2 N)$$.

There's a lower bound that says it's not possible to do any better than using $$O(t^2 \log_t N)$$ tests, so this construction is nearly optimal! Sweet and simple, yet very effective...

## Thoughts
As usual, I'd like to chime in with some ideas for how to make further progress.

**Question.** *Why do we blow up the vectors by assigning a coordinate for each letter of $$\mathbb{F}_q$$?* This seems a bit wasteful, and I wonder if there's any better way to turn our vanilla codewords into binary without sacrificing as much space. Naively I would have thought to encode each letter in binary, but I'm sure that doesn't work for some silly reason.

**Question.** *What if samples are also expensive?* The current pooling designs assume that pooled samples are cheap and tests are expensive. This was certainly true of the original application to detecting disease: doing the chemical tests in a lab was far more expensive than pooling blood from patients. However, what if the samples themselves are expensive? (For instance, if we're testing for defective items at a factory, and the items can only be tested in batches of a limited size?) What if there are also limits on how many times an item can be tested? (In the current scheme, there's nothing to prevent someone from participating in every single test, and maybe people will get tired from getting needles stuck into them every day.) I think it would be pretty interesting to look at variants of the group testing problem with additional constraints on testing capacity. But I'm sure somewhere out there, somebody has already done research on that...
