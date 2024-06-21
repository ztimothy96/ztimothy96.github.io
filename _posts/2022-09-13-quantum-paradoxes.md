---
layout: post
title: "Some quantum paradoxes"
subtitle: "Misali's paradox taxonomy"
thumbnail-img: /assets/img/quantum.png
share-img: /assets/img/quantum.png
tags: [showerthought]
---

Earlier today, Jan Misali released a video titled [The Five Kinds of Paradox](https://www.youtube.com/watch?v=ppX7Qjbe6BM), in which he points out that most things people call "paradoxes"... aren't. Instead of real logical contradictions, they're often facts (or fallacies) that sometimes don't make intuitive sense. As the title suggests, Misali found five kinds of paradoxes floating around in the wild:

1. **Logical contradictions.** These are genuine problems in logic, such as [Russell's paradox](https://en.wikipedia.org/wiki/Russell%27s_paradox) in set theory, or the [unexpected hanging paradox](https://en.wikipedia.org/wiki/Unexpected_hanging_paradox) in decision theory. These "true paradoxes" have inspired mathematicians and philosophers to develop better forms of logic, like first-order logic and type theory.

2. **Unanswerable questions.** These are questions that might have many possible answers, but it's impossible to know for sure which is right. Famously, [Schrodinger's cat](https://en.wikipedia.org/wiki/Schrödinger%27s_cat) is an example of such an impossible question: if quantum states don't collapse until observed, and we hide a cat in a box together with a quantumly-triggered bomb, then does the cat stay in a superposition of alive and dead states until someone opens the box? There are many good resolutions to this problem, none of which require the cat to stay in an absurd superposition. For instance, the cat itself might observe the quantum particle when the bomb goes off, or perhaps the bomb itself observes the particle when it is triggered. But it's hard to tell which of the solutions is true when we can't peek inside the box to find out!

3. **True paradoxes.** These are statements that appear to be false, even though they're true. This category includes lovely gems such as the [Monty Hall problem](https://en.wikipedia.org/wiki/Monty_Hall_problem) and the [St. Petersburg paradox](https://en.wikipedia.org/wiki/St._Petersburg_paradox) in probability theory. While counterintuitive facts might feel troubling at first, they demonstrate how logic serves as a useful guide, even when our gut instincts lead us astray!

4. **False paradoxes.** These are statements that appear to be false, because... they are. The arguments used to support them contain logical flaws but hide these flaws subtly. For instance, there's the inductive "proof" that [all horses are the same color](https://en.wikipedia.org/wiki/All_horses_are_the_same_color), or that [$$\pi = 4$$](https://en.wikipedia.org/wiki/Staircase_paradox), or that [all triangles are equilateral](https://www.cut-the-knot.org/Curriculum/Fallacies/AllTrianglesIsosceles.shtml). These cute little pranks can help beginning students avoid pitfalls and learn to use logic correctly.

5. **Non-paradoxes.** This last category is a bit weird: Misali describes such a paradox as a "situation that doesn't appear to contradict itself at all, and which really isn't that hard to explain, but this one time someone thought it was really confusing, and had a lot to say about it, so now people call it a paradox." Consider the [temperature paradox](https://en.wikipedia.org/wiki/Temperature_paradox):

    >The temperature is rising.
    >
    >The temperature is ninety.
    >
    >Therefore, ninety is rising. (invalid conclusion)

    The argument falls apart when it uses the word "is" in two different ways, creating an equivocation fallacy. This paradox points out a common ambiguity in natural language, rather than a problem with logic itself.

Misali's video essay got me thinking: how do his categories apply to paradoxes in quantum mechanics? Many people don't understand the quantum world very well, so sometimes you'll hear dramatic claims about how "quantum mechanics defies logic!" So while the theory seems logically consistent enough to working physicists, a fair share of quantum paradoxes has nonetheless crept into the consciousness of the general public. What kind of paradoxes are they?

Let's start simple with what I like to call the qubit paradox.

## The qubit paradox

*How can a qubit be both $$ \vert 0 \rangle$$ and $$ \vert 1 \rangle$$ at the same time?*

This paradox unfortunately gets tossed around often in popular science articles. As recently as 2022, a [Forbes article](https://www.forbes.com/sites/chuckbrooks/2022/07/20/the-quantum-era-is-arriving-and-it-will-be-transformational-/?sh=4badec5b4e7a) introduced qubits as follows.

> In a more basic description, quantum computers use quantum bits or qubits instead of using binary traditional bits of ones and zeros for digital communications. Quantum computers use atoms as a physical system allows an atom to be in both 0 and 1 states simultaneously.

This sounds like a logical contradiction; how can a particle be in two different states at once? Actually, it can't. Brooks should have instead written that a particle can lie in a *superposition* of two states. Just as the number $$1/2$$ lies between $$0$$ and $$1$$, but is distinct from each, the vector $$\frac{1}{\sqrt{2}}(\vert 0 \rangle + i \vert 1 \rangle)$$ lies somewhere between the $$ \vert 0 \rangle $$ and $$ \vert 1 \rangle$$ states. The only difference is that qubits live in a complex vector space, rather than a familiar number line. But since many people feel frightened by mathematical concepts, science writers like Brooks will make misleading statements for the sake of simpler exposition.

Since the qubit paradox stems from loosely worded science journalism, and it is very easily resolved, I think it falls under category 5 of Misali's paradox taxonomy. It is hardly a paradox at all. As for what mathematical superpositions correspond to in physical reality, that's the question behind Schrodinger's cat. As I've mentioned already, this question doesn't defy logic -- it's merely unanswerable.

Okay, let's tackle something slightly trickier: [particle-wave duality](https://en.wikipedia.org/wiki/Wave–particle_duality).

## Particle-wave duality 

*How can light be both a particle and a wave?*

This paradox seems more puzzling because it's actually true; light *does* appear to have a dual particle-wave nature. This paradox was even puzzling to physicists for many years. However, to understand what they were so worried about, we have to first understand what it means to "be" a particle in the first place.

Sometimes people unfamiliar with mathematical models make the mistake of taking them literally. In physics, when we say that an object "is" a particle, we don't mean that it literally is one, but rather that it behaves like we'd expect it to if we model it as such. Physicists are keenly aware that mathematical models only approximate reality, and different models are appropriate in different situations. This flaw isn't anything specific to logic or mathematics; *any* model, whether psychological or philosophical or something else, will dramatically simplify our world so that our puny human brains can comprehend it.

But while [all models are wrong](https://en.wikipedia.org/wiki/All_models_are_wrong), some are useful! So far, formal mathematical models of physics have proven far more successful in practice than their alternatives. When we fire a cannonball into the air and compute its trajectory, we don't really believe that it's a volumeless point mass, or that it experiences no friction or air resistance, or that the earth is flat. We're merely saying that the friction, air resistance, and curvature of the earth are negligible in this scenario, and ignoring them will make calculations easier without sacrificing much precision. The cannonball might not literally be a tiny point in a Platonic vector space, but it sure behaves like one!

What particle-wave duality really means is that "sometimes, it's useful to model light as a particle, and sometimes as a wave." When physicists say that a particle does not have wavelength, that doesn't mean they really have no wavelength, but that these wavelengths don't help us to solve the problem at hand. We can compute the trajectory of the cannonball without caring about tiny waves it makes during its travel. Similarly, when we say that waves don't have mass, we don't really mean that they literally have zero mass. Most waves, such as ripples of water or vibrating strings, obviously have mass, but it's irrelevant to answering questions about their amplitudes and frequencies. Waves and particles aren't mutually exclusive by definition, but in most classical applications, trying to combine them only results in a needlessly complicated mess.

With all that explanation, Einstein's description of particle-wave duality starts to make more sense:

> It seems as though we must use sometimes the one theory and sometimes the other, while at times we may use either. We are faced with a new kind of difficulty. We have two contradictory pictures of reality; separately neither of them fully explains the phenomena of light, but together they do.

The dual nature of light did not trouble physicists because of some perceived "contradiction" between the two models' definitions, but rather because neither could fully explain the behavior of photons. Due to this gaping hole in physics, scientists finally found the motivation to combine waves and particles into an even more general and powerful theory: quantum mechanics. Wave-particle duality perfectly illustrates how science has benefitted enormously from efforts to resolve apparent paradoxes. Without quantum mechanics, we'd be missing half of modern physics!

I'd put down this paradox as a counterintuitive fact. Quantum mechanics seems logically consistent and has been repeatedly confirmed by experiments, but it feels weird because our brains didn't evolve to think about such things. Humans survived by navigating through wavy water and particulate pebbles, not photons moving at light speed.

Genuine type 1 paradoxes do exist, but they appear much more rarely than we might initially expect. So far, it seems that paradoxes in quantum mechanics don't arise from contradictions in logic itself, but rather from its misuse and misinterpretations. It's all well and good to criticize mathematical models of physics (I'll be the first to admit they have limitations), but these criticisms would sound more convincing if they demonstrated an understanding of what the models _mean_. 