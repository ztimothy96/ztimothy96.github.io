---
layout: post
title: "1, 2, 3: An introduction"
subtitle: ""
cover-img: ""
thumbnail-img: /assets/img/baby.png
share-img: /assets/img/baby.jpg
tags: [math, test]
---

Welcome to my blog! I started writing for fun in 2019, when I first started graduate school, so that I could share some interesting computer science and math with my friends. Although I'm not in graduate school anymore, I still enjoy recording my random thoughts. To kick off this new website, I'm reposting the first blog post I ever wrote.

## Showerthought

_Why do we teach babies about the natural numbers $$\mathbb{N}$$ first?_

I don't understand what's so important about $$\mathbb{N}$$. It's not particularly interesting algebraically. Many number systems (such as $$\mathbb{Z}, \mathbb{Q}, \mathbb{R}, \mathbb{C}$$) are groups, but $$\mathbb{N}$$ isn't, as it doesn't have inverses. Heck, $$(\mathbb{N}, +)$$ isn't even a monoid, as it doesn't include $$0$$.

Maybe it's the simplicity of $$\mathbb{N}$$ itself that makes it appealing. Babies don't yet need to know about how the operation $$+$$ works, but they do understand the successor function and the total ordering it induces. I would imagine that babies innately understand that $$2 < 3$$, even if they may not understand that $$1 + 2 = 3$$.

But simplicity alone can't explain everything. Arguably, $$\mathbb{Z}/2\mathbb{Z}$$ is a much simpler number system than $$\mathbb{N}$$, as it only has $$2$$ elements. Moreover, it is not just a group, but a _field_, making it much nicer to work with algebraically. Why don't we start by teaching babies that $$1 + 1 \equiv 0$$?

Perhaps, some might argue, $$\mathbb{N}$$ is more important than those other weird creatures due to its many practical applications: "Babies need total orderings, so they'll know if someone steals their money!" (Not that they can do anything about it, or that they have any money.) But other number systems are also everywhere. Babies discover $$\mathbb{Z}/2\mathbb{Z}$$ when as they start playing with light switches, and they venture off to $$\mathbb{Z}/12\mathbb{Z}$$ when they learn to read clocks.\* Children who love playing with string might even stumble upon free groups as fundamental groups of two circles... yet, many children never go on to learn about such numbers, at least not formally in school.

![baby](/assets/img/baby.jpg){: .mx-auto.d-block :}
*Here is a baby discovering quaternions. Clever child.*

Even if we really only care about monetary applications, why use $$ \mathbb{N}$$? The monoid $$\{0, 1, ..., N-1, N\}$$ (where the successor of $$N$$ is just $$N$$, for some very large $$N$$) would be perfectly acceptable for most practical orderings. Why do we insist on creating an infinite string of new "numbers"? Having introduced this mysterious symbol $$\infty$$, why stop at "infinity is not a number," instead of throwing cardinal and ordinal numbers into the mix?

A more disturbing possibility: maybe we just collectively settled on teaching $$\mathbb{N}$ first because that's what we were taught as infants, before being marched off to its nicer extensions $$\mathbb{Z}, \mathbb{Q}, \mathbb{R}$$ and finally $$\mathbb{C}$$. Although we eventually reach (algebraic) closure, we don't often stop to smell the roses along the way. That would be kind of sad.
