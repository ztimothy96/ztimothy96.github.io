---
layout: post
title: "How to solve it"
thumbnail-img: /assets/img/solve.jpg
share-img: /assets/img/solve.jpg
tags: [advice, books]
---

## Understanding Polya's solution
I once came across a book which explains how to solve virtually any problem in life. Aptly titled [*How to Solve It*](https://en.wikipedia.org/wiki/How_to_Solve_It), it's a short little treatise on general aspects of problem-solving, which has sold over a million copies and exerted considerable influence on educators around the world. Written by the great mathematician Polya in 1945, it focuses on problems in mathematics, but Polya states the thinking process in an abstract way that extends to many other human endeavors. To solve a problem, Polya suggests a simple four-step process:

1. **Understand the problem.** If we don't understand the problem we're trying to solve, then we'll waste our time brainstorming ideas that don't work. That's why we should always begin by asking clarifying questions, questioning assumptions, gathering information, etc.

2. **Make a plan.** Once we have all the relevant information, we can start brainstorming ways to solve it. There's no point in optimizing details yet -- we're trying to rapidly generate ideas and pick out the ones that seem most promising.

3. **Carry out the plan.** After we've brainstormed many solutions to our problem, we pick one that looks promising and implement it. Here we can add little optimizations, trying our best to make it work.

4. **Look back.** The first idea might not solve our problem immediately, but we can often learn something from it. We reflect on our plan, asking: did it successfully solve the problem? If not, why not, and how can we improve on our solution? Maybe we might need to try a new plan or view the problem from a different perspective. And if we did manage to solve the problem, then we can reflect on our thought process. Why did it work, and how can we use similar techniques to solve related problems?

![solve](/assets/img/solve.jpg){: .mx-auto.d-block :}

Unfortunately, some problems are so difficult that we can't think of a plan to solve them, no matter how hard we try. Polya offers a now-famous piece of advice:

> If you can't solve a problem, then there is an easier problem you can solve: find it.

Quite often, problems only seem complicated because they're surrounded by thorny, convoluted details. By boiling away these irrelevant details, we can reveal the important insights within. That's why mathematicians keep asking questions that appear increasingly abstract and useless: they're trying to understand exactly *why* a problem is hard! After solving a simpler toy problem, they can work backward to solve the original one.

There are many tricks for boiling a difficult problem into a simpler one, and Polya explores them in detail. He calls these ideas "heuristics" and provides a nice catalog of them, including everything from analogies to induction to working backward. 

Polya's advice proved invaluable to me during my [first research project](https://proceedings.neurips.cc/paper/2019/file/785ca71d2c85e3f3774baaf438c5c6eb-Paper.pdf). I originally wanted to solve a variation of the correlation clustering problem, but there were too many possible clusterings to think about. So instead, I looked at the related [multiway cut problem](https://math.mit.edu/~goemans/18434S06/multicuts-brian.pdf), which asks how to cut a graph into pieces that separate many pairs of vertices. There were too many pairs of vertices, so I tried to separate just a single pair. Then there were still too many kinds of graphs to think about, so I decided to only look at a special family of grid-like graphs. Finally, the problem was so simple that even I was able to solve it -- but then, by using a neat trick called [padded decompositions](https://home.ttic.edu/~yury/courses/geometry/notes/metric-decomposition.pdf), I was able to reconstruct a solution to the original clustering problem! It was amazing to me that my toy problem could reveal so much information about graph clustering in general.

Toy problems play critical roles in various branches of human inquiry, far beyond mathematics. In the sciences, experiments simplify the natural world by controlling for confounding variables, thereby shedding light on casual relationships. Maybe feathers don't normally fall inside frictionless vacuums, but performing this experiment helped physicists to understand how gravity works. Similarly, philosophical thought experiments test our intuitions and help us justify our values. Even if we can't ever know the consequences of our actions with the certainty of a hypothetical trolley problem, that's beside the point. The thought experiment raises all sorts of questions about our ethical reasoning -- for instance, *why* do we care about the consequences of an action? In nearly every problem-solving domain, simple problems illuminate important concepts that hold the key to solving more complicated ones.

## Problems at play

Problem-solving doesn't only happen in academic environments. As a teenager, I realized that I could apply Polya's strategy to my many interests, often rediscovering his advice for myself. Notably, I spent countless childhood hours reading chess books such as Kotov's *Think Like a Grandmaster*, trying to get inside the author's mind. After reflecting for years to reach a 2000+ rating, I finally realized that chess masters also followed a similar four-step process:

1. **Understand the position.** In chess, we want to checkmate the enemy king, but it's unclear how to reach this goal. That's why we start by evaluating the relative strengths and weaknesses in each position -- we have to understand the available resources before we can come up with a strategy.

2. **Make a plan.** Having assessed the imbalances in the position, we can form a high-level plan. For instance, if an isolated pawn looks weak, we can maneuver our pieces to coordinate an attack on it. This will tie down the enemy pieces to passive defense, and then we can iterate and form a new plan.

3. **Carry out the plan.** Once we've decided on a high-level plan, we figure out how to implement it. How can each of our pieces help us to attack the isolated pawn? Perhaps we want to double our rooks against the pawn, and blockade our target with a knight; which order should we carry out these tasks? Here we can try to optimize for time efficiency, accomplishing the objective with fewer moves. If one of our candidate moves creates threats against the opponent, we might play that first to delay them from carrying out their plans.

4. **Look back.** After calculating variations to make sure our candidate move achieves the desired effect, we take a moment to clear the mind. A good player will make sure they don't hang pieces or do something stupid. After a sanity check, we can finally make our move and hit the clock. Next turn!

Similarly, I stumbled across the same thought process through my years of practicing piano. While I didn't explicitly make the connection to mathematics and chess until high school, I still followed the same four steps in my daily routine:

1. **Understand the music.** I practice music to create a convincing emotional performance. Listening back to my recordings, I'd identify potential areas for improvement. How could I make my interpretation sound more heartfelt? If I had trouble playing a particular passage, where did the technical difficulty come from? I'd try to pinpoint the problem as precisely as possible -- down to a particular measure, or even a jump between two notes.

2. **Make a plan.** After understanding the source of my difficulty, how could I get around it? For instance, Chopin's Etude Op. 10 No. 1 features extended arpeggios which are too wide to reach, so I'd rotate my wrist to increase my range. That means I'd have to train my wrist to remember the different degrees of rotation for each chord. If I practiced everything slowly, then I wouldn't be able to factor in the momentum of my arm traveling up and down the keyboard. Instead, I'd probably practice in altered dotted rhythms, so that I could keep some momentum while also memorizing the distance between individual pairs of notes.

    Strangely enough, music performance consists mostly of solving small physics problems.

3. **Carry out the plan.** Practice 40 hours every day!

4. **Look back.** Having learned a small section of the music, I would ask whether any new problems had appeared during my practice sessions. If not, I'd expand my scope and integrate it with the rest of the piece. Pretty often I'd jump around working on different parts of the music because practicing one bar a hundred times can distort the way I hear it.

After learning these general problem-solving strategies from math, chess, and music, I put down Polya's book and forgot about it for a couple of years. Then I started working in the tech industry...

## Problems at work

Since I started working in industry, I've noticed that Polya's mathematical advice also applies to interviews and the world of work. In the classic text, *Cracking the Coding Interview*, Gayle McDowell suggests a five-step approach to solving algorithm interview questions. But if we combine her steps of "write pseudo-code" and "write code", then we're left with an eerily similar four-step process:

1. **Understand the problem.** Once again, before we begin to solve a problem, we must understand what the problem is asking. While some engineers might think that algorithmic problems have clearly defined answers, McDowell argues otherwise:

    > Technical problems are more ambiguous than they might appear, so make sure to ask questions to resolve anything that might be unclear or ambiguous. You may eventually wind up with a very different – or much easier – problem than you had initially thought. In fact, many interviewers... will specifically test to see if you ask good questions.

    Many of the questions she suggests asking ("What are the data types? How much data is there? What assumptions do you need to solve the problem?") sound similar to those asked in system design interviews, which I'll discuss later.

2. **Design an algorithm.** Once we understand the math problem, we can think of an algorithm to solve it. At this stage, we might think about factors like correctness, runtime, and space complexity. These tradeoffs help us pick the most promising solutions.

3. **Write (pseudo-)code.** After we've decided on an algorithm, it's time to implement it in detail. We pick a programming language and write each line of code, thinking about style and clarity. We can also make slight optimizations like switch controls to make the code run faster, even if they don't reflect in high-level considerations like big-O complexity.

4. **Test.** In coding interviews, we look back on our solution by testing it! If we notice bugs in our code, we can think harder about why it's failing and fix it. We might also think about edge cases and other issues.

More recently, I've noticed that many engineers seem to dislike Leetcode and instead favor system design interviews because it's more relevant to true software engineering work. Supposedly, this is because system design interviews mimic the engineering thought process. In *System Design Interview: An Insider's Guide*, Alex Xu echoes this sentiment: 

> The system design interview simulates real-life problem solving where two co-workers collaborate on an ambiguous problem and come up with a solution that meets their goals. The problem is open-ended, and there is no perfect answer. The final design is less important compared to the work you put in the design process. This allows you to demonstrate your design skill, defend your design choices, and respond to feedback in a constructive manner...
>
> Many think that system design interview is all about a person's technical design skills. It is much more than that. An effective system design interview gives strong signals about a person's ability to collaborate, to work under pressure, and to resolve ambiguity constructively. The ability to ask good questions is also an essential skill, and many interviewers specifically look for this skill.

After reading through most of the book, I did learn plenty of domain-specific knowledge about how to write a software application. However, the problem-solving process wasn't exactly anything new. By now, you can probably guess the four-step process Xu suggests, without even opening his book:

1. **Understand the problem.**  In system design, we need to establish scope and figure out what the system should do. Xu suggests asking some questions, which are basically system-level analogs of what McDowell suggested for coding interviews: "What specific features are we going to build? How many users does the product have? How fast does the company anticipate to scale up?" And so on.

2. **Design a system.** Now that we know what we want the product to do, we can sketch a software architecture that can perform those tasks. Just like before, we should think about the tradeoffs between different goals such as availability, reliability, scalability, etc.

3. **Implement system details.** Once we have a design, we need to figure out how to implement it in detail. We can go through our architecture and optimize its components. What kind of database should we use, and what should its schema look like? How should we organize the cache, if we need one? We can add niceties, like rate limiters and load balancers, that make the system perform better.

4. **Look back.** As always, we can review our design and learn from it. We might look for systemic failures by looking at error cases, bottlenecks, or future scaling concerns. We might summarize the main ideas leading to the design and try to extend them for different applications.

And would you look at that, Polya's four-step process works for everything! Since he talks about problem-solving in abstract terms, his advice applies everywhere from music to software engineering. *How to Solve It* might cover plenty of mathematics, but it easily reads as life advice, and that's why it's so remained relevant in education over the years.

## Looking forward

While I love Polya's book very much and find its advice useful, I wouldn't pick up new problems just so that I could apply his four-step process over and over. For instance, I don't enjoy playing chess as much anymore, even though it poses problems to solve. Once I figured out how Polya's principles applied to the game, leveling up started feeling like a repetitive grind rather than a creative pursuit. Since I've already spent so much time practicing how to solve problems, I care more about solving *particular* problems that are important and beautiful.

I would like to spend more time thinking about where else I can apply this general problem-solving process. Human brains compartmentalize naturally, so even if we've learned to problem-solve in one domain, we might forget about those skills in a new environment. After all, I independently rediscovered Polya's ideas three times, in the contexts of math, music, and chess. It took many years before I found the shared principles between them, and it took me even longer to realize that I could apply these ideas to other pursuits. Similarly, other people might have learned about problem-solving in the contexts of software engineering and psychology, but appear blind to the same concepts when they're presented in mathematics or music. Pretty much everyone has a blind spot, and it can be challenging to spot them by ourselves. Maybe it takes conscious effort to remember Polya's four steps whenever we land in a new problem domain...