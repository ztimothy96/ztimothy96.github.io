---
layout: post
title: "1, 2, 3: An introduction"
thumbnail-img: /assets/img/baby.png
share-img: /assets/img/baby.png
tags: [math, showerthought]
---

This is my first blog post! Hooray!

I guess I should introduce the purpose of this blog and my plans for it. I’m a soon-to-be PhD student in theoretical computer science, so I’ve been learning a few things about algorithms. Sometimes I will try to explain stuff to my friends, who are sometimes not PhD students, and then find myself without pencil and paper. So I thought, maybe instead of trying to draw CW complexes on napkins using Sriracha sauce, maybe I should just start a blog and actually write things down.

I’ll try to explain basic concepts as clearly and intuitively as I can. In this blog, I hope to make complicated ideas seem motivated, natural, and maybe even trivial (hence the name). A few warnings:

- These posts will NOT be mathematically rigorous. Read at your own risk.
- These posts will assume typical undergraduate knowledge of calculus and linear algebra, sometimes more.
- I may post random shower thoughts that pop into my head, unrelated to CS. Some of these may be rather silly.
- I may draw pictures using Paint. I am a fan of mathwithbaddrawings, so I’ve decided to make my drawings EVEN WORSE.

## Showerthought

*Why do we teach babies about the natural numbers $$\mathbb{N}$$ first?*

![baby](/assets/img/baby.png){: .mx-auto.d-block :}

I don’t understand what’s so important about $$\mathbb{N}$$. It’s not particularly interesting algebraically. Many number systems (such as $$\mathbb{Z}, \mathbb{Q}, \mathbb{R}, \mathbb{C}$$) are groups, but $$\mathbb{N}$$ isn’t, as inverses don’t exist. Heck, $$(\mathbb{N}, +)$$ isn’t even a monoid, as it doesn’t include $$0$$.

Maybe it’s the simplicity of $$\mathbb{N}$$ itself that makes it appealing. Babies don’t yet need to know about how the operation $$+$$ works, but they do understand the successor function and the total ordering it induces. I would imagine that a baby innately understands that $$2 < 3$$, even if it may not understand that $$1 + 2 = 3$$.

But simplicity alone can’t explain everything. Arguably, $$\mathbb{Z}/2\mathbb{Z}$$ is a much simpler number system than $$\mathbb{N}$$, as it only has two elements. Moreover, it is not just a group, but a field, making it much nicer to work with algebraically. Why don’t we start by teaching babies that $$1 + 1 \equiv 0$$?

Perhaps, some might argue, $$\mathbb{N}$$ is more important than those other weird creatures due to its ubiquity in practical applications: “Babies need total orderings, so they’ll know if someone steals their money!” (Not that they can do anything about it, or that they have any money.) But other number systems are also everywhere; babies discover $$\mathbb{Z}/2\mathbb{Z}$$ when as they start playing with light switches, and they venture off to $$\mathbb{Z}/12\mathbb{Z}$$ when they learn to read clocks. Children who love playing with string might even stumble upon $$F_{a, b}$$ as the fundamental group of two circles. And here in the US, many children never go on to learn about such numbers, at least not formally in school.

And even if we really only care about monetary applications, why use $$\mathbb{N}$$? The monoid $$\{0, 1, ..., N-1, N\}$$ (where the successor of N is just N, for some very large $$N$$) would be perfectly acceptable for most practical orderings. Why do we insist on creating an infinite string of new “numbers”?  Having introduced this mysterious symbol $$\infty$$, why stop at “infinity is not a number,” instead of throwing cardinal and ordinal numbers into the mix?

A more disturbing possibility: maybe we just collectively settled on teaching $$\mathbb{N}$$ first because that’s what we were taught as infants, before being marched off to its nicer extensions $$\mathbb{Z}, \mathbb{Q}, \mathbb{R}$$ and finally $$\mathbb{C}$$. Although we eventually reach (algebraic) closure, we don’t often stop to smell the roses along the way. That would be kind of sad.

## P.S.
I guess $$\mathbb{Z}/7\mathbb{Z}$$ is also pretty cool, but I haven’t thought of a “practical application” for it.