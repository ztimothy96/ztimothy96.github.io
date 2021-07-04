---
layout: post
title: "tldr; Differential geometry"
subtitle: "Manifolds"
tags: [math] [geometry]
---

In college, I was pretty interested in learning about differential geometry and general relativity, but never found the time for it. Recently I started revisiting the subject, following the [lectures](https://www.youtube.com/playlist?list=PLFeEvEPtX_0S6vxxiiNPrJbLu9aK1UVC_) from the International Winter School on Gravity and Light. I'm writing mostly to clarify my own thoughts, trying to explain the main concepts as simply as I can, but maybe someone else will also find them interesting.

## Manifolds
Differential geometry is basically calculus on curvy shapes called manifolds. A manifold looks locally flat, but may be globally curved. The simplest example is euclidean space $$\mathbb{R}^d$$, which is flat everywhere. A slightly more interesting example is a sphere $$S^n$$. The earth looks flat if you look around locally, but if you keep sailing West, you will eventually get back to where you started and discovers that the earth is really curved.

Since manifolds are locally flat, you can navigate in them by drawing charts on a flat piece of paper for a small neighborhood. You can map out the entire world by drawing lots of charts and putting them together into an atlas.


> **Definition:** A topological space $$(M, \mathcal{T})$$ is a $$d$$-dimensional *topological manifold* if every point $$p \in M$$ has an open neighborhood $$U \in \mathcal{T}$$ and a map $$x : U \to \mathbb{R}$$ such that
 1. $$x$$ is invertible,
 2. $$x, x^{-1}$$ are continuous.
Here $$(U, x)$$ is a *chart*, and $$x$$ is a *chart map*. A collection of charts $$ \mathcal{A} = \{(U_\alpha, x_\alpha)_{\alpha} \}$$ covering the space $$M = \bigcup_\alpha U_\alpha$$ is called an *altas*.

(Todo: write more, but check that the formatting works first.)
