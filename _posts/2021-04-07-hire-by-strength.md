---
layout: post
title: "Hire-by-strength is set cover"
thumbnail-img: /assets/img/setcover.png
share-img: /assets/img/setcover.png
tags: [showerthought]
---

A special someone and I were discussing hire-by-strength at tech companies recently, and this connection just jumped into my mind. Does it count as applied math?

Imagine you’re running a company, and you would like to hire some people to work on some project. You’re looking for a collection of $$n$$ skills. There are $$m$$ candidates, each of whom possesses a subset of the skills you’re looking for. Since you hire by strength, you don’t hold it against the candidates if they don’t possess many skills; you’ll be satisfied with a collection of candidates as long as each skill is possessed by at least one candidate. However, you have a company to run, so you would like to hire as few candidates as possible to get the work done.

This is, of course, exactly the set cover problem where the universe consists of skills and the sets are the candidates, and the objective is to cover all the skills with the least number of candidates. (If the candidates have different expected salaries, you may want to solve the weighted version of the problem instead.)

Unfortunately for Google and Facebook, this problem is $$\text{NP}$$-hard, so they probably won’t find an optimal allocation of their budget anytime soon. In fact, the problem is even hard to approximate up to a $$\log n$$ factor. Fortunately for Google and Facebook, there is a greedy $$\log n$$-approximation algorithm: at each step, hire the candidate who covers the greatest number of currently uncovered skills. This algorithm is not only optimal up to hardness assumptions, but also simple to implement and very easy to explain in a rejection letter! I wonder if Google would actually create a checklist of skills, get interviewers to fill them out, and let the greedy algorithm do the rest?

![setcover](/assets/img/setcover.png){: .mx-auto.d-block :}
> Purple candidate knows four skills to a minimum level of competency? Great, you're hired!

## P.S.
From the perspective of an employee looking to get hired, this algorithm would encourage learning many semi-related skills to a minimum level of competency, as well as lowering your expected starting salary. Coincidentally, PhD programs would appear to encourage the exact opposite behavior...