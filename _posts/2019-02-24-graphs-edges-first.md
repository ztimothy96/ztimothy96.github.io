---
layout: post
title: "Graphs, edges first"
thumbnail-img: /assets/img/graphs/directed.png
share-img: /assets/img/graphs/directed.png
tags: [math, algorithms]
---

## What’s a graph?
When people teach graph theory, they usually introduce vertices first, then edges. It’s natural to think of graphs this way; when you sit down and try to draw a graph, it’s easier to play connect-the-dots if you draw the dots first. Or is it?

Not-so-recently I came across a nice book at [planarity.org](https://planarity.org/), which flips this approach on its head. The authors define a graph as a bunch of edges connected at vertices, rather than a bunch of vertices connected by edges! Although I initially found it rather confusing, this edges-first perspective has some major advantages, most notably a simpler way to look at graph duality. So here’s my attempt at explaining how it works.

Our goal here will be to build a (directed) graph from a ground set of edges $$E$$. We give each edge $$e$$ a direction by putting darts on it. The primary dart $$e^+$$ points in the direction of $$e$$, while $$e^-$$ points the other way. These two darts are reverses of each other. We can write down a reversal function, $$rev(e^+) = e^-$$ and vice versa. The dart set of $$E$$ is the collection of all these darts, $$E \times \{+1, -1\}$$. Now we just need to connect the edges to make a graph.

![darts](/assets/img/graphs/darts.png){: .mx-auto.d-block :}
> An edge $$e$$ with its darts. The dartboard on the right is definitely not an advertisement.

Since a vertex is a place where edges stick together, we can specify a vertex by listing all of darts that point to it. Formally speaking, $$V$$ is a partition of $$E \times \{+1, -1\}$$, and each vertex $$v$$ is a piece of this partition. Putting them together, we get a graph, $$G = (V, E)$$.

![directed](/assets/img/graphs/directed.png){: .mx-auto.d-block :}
> A directed graph $$G$$. Vertex $$v$$ consists of darts $$\{a^+, b^+, c^+, d^-\}$$. What is vertex $$x$$?

Why would anybody bother to go through all this trouble? It turns out, this alternate perspective is very useful for talking about faces and graph embeddings!

## Drawing flat things
One of the most special things about a planar graph is that, once you draw it inside the plane, you can talk about its faces. Usually the definition for a face is something like “a region of the plane which is enclosed by the edges.” However, this definition makes proving anything about planar graphs very, very hard, for a few reasons:

- It requires lots of topology. Even intuitively obvious facts, like “faces exist,” can be rather tricky to prove.
- It’s too complicated. To write down an embedding, we have to specify some mappings $$f: G \to \mathbb{R}^2$$, but we don’t really care about the maps themselves – just the way the edges wind around each other.

How can we represent an embedded graph $$G$$ more efficiently? This natural question leads us to rotation systems. Notice that if we embed $$G$$ into the plane, then at every vertex $$v$$ we can list the cycle of darts around $$v$$ in counter-clockwise order.

![directed](/assets/img/graphs/directed.png){: .mx-auto.d-block :}
> For this particular embedding, $$\pi(a^+) = b^+, \pi(b^+) = c^+, \pi(c^+) = d^-, and \pi(d^-) = a^+$$. Then $$v$$ is the orbit $$(a^+, b^+, c^+, d^-)$$.

I’m going to skip a few technical details, wave my hands in the air, and just define $$(\pi, E)$$ to be an embedded graph. Here $$\pi$$ is the “turn-counter-clockwise” function. Each vertex $$v$$ is just an orbit of $$\pi$$; it’s all the places you can get to via turning around a starting dart.

Now that we can draw planar graphs, it’s time to define faces.

## Around and around

Intuitively, we can specify a face by walking clockwise around its boundary until we hit all its edges. How to define this mathematically? Let’s say we’re starting at a dart $$d$$. To see the next dart on the face, we need to turn counterclockwise to $$\pi(d)$$. Then we need to walk away from the starting dart, so we end up at $$rev \circ \pi (d)$$. In short, the “walk clockwise” function is given by $$\pi^* = rev \circ \pi$$. A face $$f$$ is just an orbit of $$\pi^*$$; it’s all the places you can get to via walking clockwise from a starting dart.

![directed](/assets/img/graphs/directed.png){: .mx-auto.d-block :}

> Evidently this graph is a silly example: $$\pi^*(a^+) = b^-$$, and $$\pi^*(b^-) = a^+$$. So there’s a two-sided face given by the orbit $$(a^+, b^-).$$

Notice that vertices and faces seem closely related: both are orbits of some kind of rotation. This relationship is called graph duality, and it’s probably one of the coolest things about planar graphs. But that’s enough for today.