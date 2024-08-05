---
layout: post
title: "Baba is You is math"
thumbnail-img: /assets/img/baba.png
share-img: /assets/img/baba.png
tags: [math, games]
---

I've been sort of busy, but lately, a friend introduced me to a cute-looking puzzle game called "Baba is You." Being a sucker for cute things and logic puzzles, I immediately took the bait. (Thanks, Kathleen.) After 20 hours of playtime, five completed worlds, and endgame content unlocked, I had to stop to think about why the puzzle design appealed to me so much.

Turns out, playing Baba is You feels exactly like doing math research. Ah, how I have missed this experience! 

Some other gamers have already written extensively about the similarities between [Factorio and software engineering](https://www.youtube.com/watch?v=vPdUjLqC15Q&t=1s), and I think we can make a similar parallel between Baba is You and math. In this post, I'd like to explore some of these connections, including:
- formal systems
- abstraction and recursion
- lateral thinking
- difficulty.

Let's get started!

## What is Baba is You?
You can find the premise of Baba is You on any number of resources on the Internet. I recommend checking out the [gameplay trailer](https://www.youtube.com/watch?v=z3_yA4HTJfs). There are objects on the screen, including a sheeplike creature named "Baba". Some of them might be "You", meaning that the player can control their movement. Block can also contain words that describe the rules of the game; for instance, three blocks that say "Baba is You" would make a Baba object into a playable object. Hilariously (and this is a key gameplay mechanic): we can rearrange these text blocks around to form new rules. The player clears a level by creating a rule reading "\[X\] is Win" for some object **X** and making the **You** object collide with it. Throughout this post, I will refer to the game's rule-making language as "Babacode."

So what does this silly little puzzle game have to do with math? you might ask. Well, the very concept of "Babacode" should give it away.

## Formal systems
At a most basic, literal level, we might notice that Baba is You is a game about pushing logical blocks around to create some desired statement (namely, "X is Win"). In short, it is a [formal system](https://en.wikipedia.org/wiki/Formal_system), where the formal language consists of all the sentences that are valid in the game syntax, usually of the form "\[Noun\] \[Operator\] \[Property\]". Unusually, the deductive system (by which we transform previous rules into new ones) requires the player to literally manipulate blocks of words in-game.

Like Baba is You, mathematics is also a formal system, in which we take existing logical statements and use them to infer new ones. This leads to several natural parallels between the two systems.
- _Both are open-ended._ Initially, there seems to be no limit to what we can express in either mathematics or Babacode, besides the limits of computability itself. (I suspect that Babacode is probably Turing-complete.) Indeed, some brave soul even implemented [Pacman](https://www.youtube.com/watch?v=97BhtVA0E_0) using Baba syntax! 
- _Both have axioms._ In mathematics, axioms are starting rules that we choose to accept without proof. It might seem that Baba is You might have no axioms since the player can rearrange word blocks to destroy rules, but it turns out that we can prevent this by surrounding words with barrier objects or by putting them in an inaccessible corner. Once we have fixed some axioms, then the level becomes more restricted and there are only a few ways to win.
- _Both seek inferences._ Mathematicians and Baba players alike are trying to manipulate existing logical rules to create new, interesting ones; mathematicians call these "theorems," while Baba players call them "win conditions." In both formal systems, often it's not immediately clear what statement we're trying to prove; we might have to grope around in the dark to explore what is possible within the system. But more on this later.
- _Both use chunking._ When playing Baba is You, we might realize that a solution that we've seen on a previous level can be reused as a smaller step towards solving a more complicated level. This is similar to how mathematicians reuse previous results (lemmas, theorems) as steps towards forming proofs of new theorems.

So much for the similarities in the base game mechanics. Perhaps more surprisingly, Baba is You can also illustrate key concepts within mathematics and computer science proper.

## Abstraction and recursion

Baba is You reminds me a lot about [category theory](https://en.wikipedia.org/wiki/Category_theory), a branch of math so abstract and recursive that people who work on it lovingly refer to it as ["abstract nonsense"](https://en.wikipedia.org/wiki/Abstract_nonsense). For the uninitiated, category theory is a branch of math *about mathematics*; it abstracts away mathematical objects (which are in themselves already fairly abstract) into simply "objects," and it only talks about "arrows" or relationships between them. Surprisingly, this is enough to capture a fair amount of information about the behavior of the objects involved; perhaps less surprisingly, this general theory about how to talk about objects and their interactions can aid in the design of [functional programming languages](https://en.wikipedia.org/wiki/Functional_programming) like Haskell.

Is Babacode a functional programming language? I haven't thought too hard about it, but I *have* thought about ways that the game instantiates category-theoretic concepts.
- _Objects are abstract._ Just as in category theory, objects in Baba is You have no intrinsic properties; instead, their meaning is entirely determined by their interaction ("arrows") with other objects, as specified by the game rules. There is no distinction between a **Key** and a **Door** aside from the fact that a **Key** is not a **Door** and vice versa. If we swapped all instances of keys and doors and also swapped all the rules governing those keys and doors, the solution to the level would not change. It is entirely possible to clear a level by forming the rules "Key is Shut" and "Door is Open" and pushing the door towards the key to open it; in fact, much of the gameplay heavily relies on exploiting this interchangeability between objects.
- _Objects form hierarchies._ I don't know exactly how to describe this aspect of the game, as I don't study programming language design, but in any case, Baba is You often plays with the ideas of objects, classes, and their instantiations. For instance, throughout much of the game, players can only form rules by rearranging **Text** blocks to endow a class of objects with some property. However, in the level titled "Metacognition," we learn that it is possible to turn an instance of a class into a **Word**, which can then be used to form new rules!

    (To make this more concrete: I can form a rule saying "Baba is Word." Then I can move an instance of **Baba** next to "is Defeat" to form the rule, "Baba is Defeat." And no, this is not much of a spoiler, as you'll quickly find out if you play the level.)

    This sort of interplay, using game elements as source code and objects simultaneously, reminds me of similar designs in programming languages. It's similar to how a Python **function** is also an **object**, just like any **int** or **list**, and therefore you can do other functions to them and manipulate their memory addresses. Of course, in software engineering, we heavily frown upon such manipulations for the same reason that we never use "goto" or "eval" statements: they create unstructured programs that pose security threats and are nightmarishly difficult to maintain in large-scale systems. But in Baba is You (and in mathematics!), such mind-bending exploits are common and even encouraged. But more on this later.

- _Systems are recursive._ Category theory, as the "mathematics of mathematics," is a famously self-referential topic to study. It's wild enough to abstract away a whole branch of mathematics into a category containing objects and arrows, but what happens if we treat a category itself as an object? We'd end up creating **Cat**, the [category of categories](https://en.wikipedia.org/wiki/Category_of_small_categories), where the objects are categories and the arrows between them are called "functors." Then we could use this new concept, "functor," to create [functor categories](https://en.wikipedia.org/wiki/Functor_category) where the objects are functors and the arrows between them are "natural transformations." And this can keep going, off to infinity, to create [infinity-categories](https://en.wikipedia.org/wiki/Higher_category_theory).

    (In case you're wondering: yes, this "abstract nonsense" actually has applications, though mostly within math and CS as far as I'm aware. Functors are what mathematicians use to talk about analogies between different areas of mathematics, turning objects and arrows from one branch of math into those of a different branch. More concretely, category theory originated from [algebraic topology](https://en.wikipedia.org/wiki/Algebraic_topology), where functors and natural transformations allow us to turn questions about a category of squishy topological shapes into those about a category of algebraic structures. By crunching the algebraic questions, perhaps on a computer, we can then better understand the corresponding shapes; e.g. count the number of holes in a high-dimensional cluster of points. Anyway...)

    How is Baba is You similarly self-referential? you might ask. Since I can't reveal much without giving away major spoilers, let's just say that there is a level that introduces the word "Level", enabling the player to form rules such as "Level is Win" or "Level is Move." And from there the game goes down a deep, deep rabbit hole...

Since the literal content of the game correlates so well with mathematics, playing Baba is You requires a highly mathematical mindset. I'll try to describe how the process of solving its puzzles reminded me of doing math research.

# TODO: WRITE THE REST OF THIS BEYOND OUTLINE

## Lateral thinking
- _Questioning assumptions._ The game forces you to use objects in ways that you probably didn't know they could be used.
- _Open-ended exploration._ Right from the beginning, the game encourages playing around with objects and understanding how the rules work. When you get a new word, think about how it interacts with different words; this is similar to how we explore interactions in mathematics, e.g. what happens if we put a continuous function on a compact set? And so on. (This is how I ended up discovering the hidden levels of Baba is You.)
- _Definition hacking._ Seeing the interactions between words in action can help us to refine our understanding of what words mean. Conversely, once we deeply understand the game syntax and various definitions, we can predict new possible interactions. (Think of the level "Lovely House" and its obvious connection to the [Banach-Tarski paradox](https://en.wikipedia.org/wiki/Banach%E2%80%93Tarski_paradox).)

## Difficulty
- Baba is You also seems like a game about software engineering, but structurally it is not; the paradigms of problem-solving feel rather different.
- In software engineering and Factorio, it is relatively obvious what you are supposed to do to win (retrieve some data for the app users; create a spaceship to escape the hostile planet). In principle, you know how to do it, and you could perform the task manually. The difficulty appears when we encounter these "easy problems" on a massive scale. At some point, we have to design a system to automate the tasks for us so that the process doesn't take forever. In such engineering games, there are often many possible good design solutions (but also many bad ones).
- Baba is You is the opposite. The levels are tiny, and if we knew how to solve them, we could execute the steps in a matter of seconds. (Some [speedruns](https://www.youtube.com/watch?v=7TNwFevtbdg) complete the game 100% in less than two hours). The problem is that the logic puzzles are difficult to figure out; we often don't even know what the win condition looks like, much less how to achieve it.
- The difficulty is in coming up with the right ideas. Most of the puzzles look downright impossible to solve until you see that one key trick, after which the whole thing appears trivial.
- I guess that's the main connection between Baba is You and math research: they follow much the same process. You stare at the puzzle in utter bewilderment for an hour or so, feeling that your "Brain is Melt." Then you go for a walk, take a shower, and suddenly the solution pops into your head and you feel like a genius. It's incredibly satisfying.