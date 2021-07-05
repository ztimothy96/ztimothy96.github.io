---
layout: post
title: "tldr; Differential geometry"
subtitle: "Manifolds"
tags: [math] [geometry]
---

In college, I was pretty interested in learning about differential geometry and general relativity, but never found the time for it. Recently I started revisiting the subject, following the [lectures](https://www.youtube.com/playlist?list=PLFeEvEPtX_0S6vxxiiNPrJbLu9aK1UVC_) from the International Winter School on Gravity and Light. I'm writing mostly to clarify my own thoughts, trying to explain the main concepts as simply as I can, but maybe someone else will also find them interesting.

## Smooth manifolds
Differential geometry is basically calculus on curvy shapes called manifolds. A manifold looks locally flat, but may be globally curved. The simplest example is euclidean space $$\mathbb{R}^d$$, which is flat everywhere. A slightly more interesting example is a sphere $$S^n$$. The earth looks flat if you look around locally, but if you keep sailing West, you will eventually get back to where you started and discover that the earth is really curved.

Since manifolds are locally flat, you can navigate in a small neighborhood around you by drawing charts on a flat piece of paper. You can map out the entire world by drawing lots of charts and putting them together into an atlas.

> **Definition.** A topological space $$(M, \mathcal{T})$$ is a $$d$$-dimensional *topological manifold* if every point $$p \in M$$ has an open neighborhood $$U \in \mathcal{T}$$ and a map $$x : U \to \mathbb{R}$$ such that
 1. $$x$$ is invertible,
 2. $$x, x^{-1}$$ are continuous.

>Here $$(U, x)$$ is a *chart*, and $$x$$ is a *chart map*. A collection of charts $$ \mathcal{A} = \{(U_\alpha, x_\alpha)_{\alpha} \}$$ covering the space $$M = \bigcup_\alpha U_\alpha$$ is called an *altas*.

Of course, there are many different charts you could draw for the same location (as you will discover if you flip through an actual atlas), so it's important to know how they fit together. For instance, say you're sailing from Europe to the Americas, and you have charts for each continent. You start by using the European chart to sail into the Atlantic Ocean, but then you had better be able to find your location in the American chart again if you want to continue your journey. The chart transition maps allow us to stitch different charts together.

> **Definition.** Given charts $$(U, x)$$ and $$(V, y)$$, the *chart transition map* between them is $$ y \circ x^{-1}: \mathbb{R}^d \to \mathbb{R}^d$$.

Now suppose we're doing physics, and we want to know the velocity or acceleration of our ship as we sail through the ocean. We can try to do calculus using our location in a chart, but we better make sure that the physics of our ship don't depend on the chart we decided to draw! (You should have the same acceleration whether you do the calculations using the European chart or the American one.)

For instance, we might want to know whether our ship is sailing smoothly, or whether we're making some sharp turns. Suppose that we sail along some curve $$\gamma: \mathbb{R} \to U$$, and we pick a chart $$x: U \to \mathbb{R}^d$$. We'd like to say that $$\gamma$$ is *smooth* (or $$C^{\infty}$$, or infinitely differentible) if $$x \circ \gamma$$ is. Unfortunately, the smoothness of $$\gamma$$ really depends on which chart $$(U, x)$$ we picked. To make sure we always get the same answer, we need to require that the charts are compatible with each other.

> **Definition.** Two charts $$(U, x)$$ and $$(V, y)$$ are *smoothly ($$C^\infty$$) compatible* if $$U \cap V = \emptyset$$ or the chart transitions $$y \circ x^{-1}, x \circ y^{-1}$$ are both smooth.
> A *smooth altas* is an atlas where all the charts are smoothly compatible. A *smoth manifold* $$(M, \mathcal{T}, \mathcal{A})$$ is a topological manifold with a smooth atlas.

There are other kinds of compatibility, like $$C^0$$ or $$C^1$$ compatibility instead of $$C^\infty$$. But some fancy math says that we pretty much only have to care about the latter.

> **Theorem.** Any $$C^k$$ atlas ($$k \geq 1$$) contains a $$C^{\infty}$$ atlas.

We can ask whether two manifolds look the same in terms of their smooth structure. For instance, slightly deforming the earth into an ellipsoid would not change whether our ship's path was smooth, but turning it into a cube would destroy a smooth path if it crossed over a sharp edge. Hence, ignoring mountains and crevices, the earth is the same as a ellipsoid, but not the same as a cube. This concept of equivalence is called *diffeomorphism*.

> **Definition.** A map $$\phi: M \to \N$$ between two smooth manifolds $$M, N$$ is a
*diffeomorphism* if:
1. $$\phi$$ is invertible,
2. $$\phi$$ and $$\phi^{-1}$$ are both smooth.
> We then say that $$M, N$$ are *diffeomorphic* to each other.

(To do: add diagrams.)
