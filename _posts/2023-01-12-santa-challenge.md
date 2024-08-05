---
layout: post
title: "Santa's broken printer"
subtitle: "What I learned from the 2022 Kaggle optimization challenge"
thumbnail-img: /assets/img/christmas.png
share-img: /assets/img/christmas.png
tags: [math, algorithms]
---

Last December, I stumbled across the annual Kaggle optimization challenge and decided to give it a shot. Since it was my first time attempting a concrete optimization challenge (rather than publishing papers on the theory), and due to the limited time I spent on it, I didn't manage to earn any medals. Still, I had fun in the attempt, and I managed to learn quite a bit about how optimization works in practice. Let's see how the experience went.

## Problem statement

You can find the details [here](https://www.kaggle.com/competitions/santa-2022/overview). Essentially, Santa needs to print Christmas cards, but his printer broke. Fortunately, he has a robot arm with 8 joints that can print images by moving its actuator to each of the pixels and stamping it with the right color. Alas, nothing in life is free; there's a cost for changing the color of the next pixel and for moving the robot joints. The challenge asks participants to find a sequence of arm configurations (that is, a displacement for each of the robot's joints) such that the robot will print out the image successfully while minimizing cost.

![4opt](/assets/img/printer.png){: .mx-auto.d-block :}

*In the example above, the robot arm incurs a color cost for switching from a green to a red pixel. It also incurs cost for moving one of its joints (rotated at the base).*

## My attempt

In high school, I once used a combination of genetic algorithms and local search to solve a Traveling Salesman Problem (TSP) for points on the Euclidean plane. The Kaggle challenge seemed a bit more complicated, as it featured two different kinds of costs: one for changing the color, and one for changing the robot configurations. The color cost only depended on the tour the arm took through the image, so optimizing it was equivalent to solving a TSP instance. However, the configuration cost depended heavily on the motions that the robot arm took to *reach* those points, and this search space seemed more difficult to search through.

Drawing from my previous project, I wanted to run an evolutionary algorithm combined with bi-level optimization. To evaluate the total cost of a sequence, I would first fix a TSP tour to get the color cost, find a relatively good configuration to perform that tour, and then perform an evolutionary search on the TSP tours. Unfortunately, I ran into multiple problems while trying to implement this scheme.

First of all, I needed to figure out how to compute good configurations to perform a TSP tour. As it turns out, this was enormously difficult. Not only were there 8 degrees of freedom due to the robot joints, each with dozens of possible positions, but I had to plan a sequence of such configurations for 66050 points in the image. This search space was unfathomably large, so I could not hope to find an optimal solution. Instead, I decided to greedily hop from one point to the next with the configurations that would minimize the number of changed joints at each step and managed to find such configurations with a [linear program](https://en.wikipedia.org/wiki/Linear_programming). While this method could find relatively good configurations, the solver took an hour to process the full image, meaning that I couldn't run it thousands of times in an evolutionary search. Instead, I would have to first find a good TSP tour minimizing color cost (using some variant of the $$k$$-opt heuristic), run the LP solver once, and hope that the resulting reconfiguration cost wasn't too high.

Then I moved on to implementing a TSP solver. At first, I tried to import a Python library I found on Github, but it required the distance matrix between all pairs of points. Since there were 66050 points in the image, I would have to feed it a matrix with over 4.3 *billion* entries. This took hours to compute and ate up 35 GB on my laptop, so accessing matrix entries was too slow. Deterred by this difficulty, I decided to download the third-party source code and modify it to compute color costs in memory. Unfortunately, the evaluation method given by competition sponsors took 0.4 seconds to find the color cost of a single path, far too slowly for a TSP solver. I therefore spent some time vectorizing this function in Numpy, until I whittled it down to 0.002 s (200 times faster). Finally, I could run my TSP solver! -- and then I discovered the main difficulty with optimization. Since the TSP solver didn't know anything about the reconfiguration cost, it would slightly decrease color cost, but force the robot arm into weird configurations, thus raising the overall cost. Due to this conflict between optimization objectives, I had difficulty beating the simple baseline of traversing the pixels in lexicographical order.

At this point, I decided I had spent enough time on this side project and wanted to see how top competitors approached the problem. Perhaps they knew some fancy algorithms that I didn't?

## Constraining the tour

Apparently not! The optimization challenge didn't require us to know about fancy algorithms other than basic $$k$$-opt and TSP approaches I'd already seen. However, it did require participants to uncover a few critical insights into the problem structure that could greatly speed up the search.

The first insight was that we should use the geometry of the robot arm to constrain the configuration search space. Others quickly realized what it took me several failed attempts to discover: since the color TSP solver can't easily evaluate reconfiguration costs, we should only consider TSP tours that start with the minimum possible reconfiguration costs. (Again, I joined the competition late and probably didn't spend as much time as I should have.) By considering *each quadrant of the image separately*, and constraining the robot joint movements for each quadrant, we can find configurations that ensure minimum reconfiguration cost at each step if and only if the actuator travels one pixel at a time. That is, we should only consider tours where the actuator moves up, down, left, or right at each step.

Once we had set this constraint, the problem was reduced to finding a TSP tour with minimal color cost, again where we can only travel between adjacent pixels. Since this problem remains NP-hard, we still have to perform a local search using some variation of $$k$$-opt. However, with the constraint of only moving to adjacent pixels, the standard $$2$$-opt heuristic no longer works. So instead, some Kagglers suggested using specialized versions of $$4$$-opt, which would allow us to keep the TSP tour connected. As we know edges must be between adjacent pixels, which lowers the search space from $$O(n^4)$$ to $$O(n^2)$$. When $$n=66050$$, this process can terminate within a couple of hours.

![4opt](/assets/img/4opt.png){: .mx-auto.d-block :}

With this basic strategy in place, we could try many different optimizations to improve the tour. For instance, we could try to stitch together tours from different quadrants at a shared boundary point, where the robot configuration wouldn't have to change very much. We could experiment with different types of $$4$$-opt perturbations to the best solution found so far. Once we found some favorable swaps with $$4$$-opt, we could also play around with the choice of swap. (Do we perform the first swap we find, or do we randomly pick one? Maybe it would be faster to find high-cost edges of the tour and try to swap them out first.) Rather than building our own custom TSP solver, we could instead import some heavy-duty libraries like [LKH](http://akira.ruc.dk/~keld/research/LKH-3/). All of these little optimizations add up to dramatically improve our TSP tour.

## What I learned

Now that we've seen how the top performers approached this optimization challenge, what can I learn from this experience?

Probably the most important takeaway: these challenges are not about knowing fancy algorithms, but rather creatively applying well-known algorithms to exploit structure in the specific problem at hand. I say "creatively" because finding this structure requires brainstorming a few novel, nonobvious insights. Here, we faced the difficulty of optimizing both color and configuration costs at once. To overcome this obstacle, we had to realize that we could exploit the geometry of the robot arm by:
1. splitting the image into quadrants, and 
2. converting short pixel distances into joint reconfiguration distances in each quadrant. 

Armed with this novel insight, we could *constrain* ourselves to use TSP tours that only traveled to adjacent pixels, removing one of the optimization objectives! Although it was not initially clear that this strategy was even possible, it turned out to be key to the whole problem! (Would it still work if we changed the length of the robot joints?)

This challenge again illustrates the value of solving toy problems: they reduce complexity and illuminate solutions. Some might object that only looking at tours with perfectly optimal reconfiguration costs might overlook better solutions that sacrifice a bit of joint movement for improved color cost, but that's overkill. We don't need a perfect solution; we just need one that's good enough and easy to find. By forcing the actuator to move only one pixel at a time, we may have eliminated some perfect solutions, but we also eliminated far more bad solutions, which made it easier to find a good one. Some of the top Kaggle submissions came within 3% of a theoretically perfect score; that's pretty impressive!

Sometimes, we may also need to use suboptimal algorithms due to time constraints. Speed matters greatly when tackling large-scale optimization problems. While I had originally intended to run an evolutionary search, I soon realized that evaluating reconfiguration cost would take far too much time, and eventually switched to local search techniques like $$k$$-opt. If I had realized this from the start, I would have been more likely to reduce the problem right away, instead of spending my time testing an LP solver (which ultimately didn't help at all). I can't fault myself for writing the LP solver, since I didn't know any better and that was part of the experimental process. But for future optimization challenges, I'd think harder about the time costs involved in my optimization approach.

Finally: I realized that it's been a long time since I've written any algorithmically heavy code, so my mathematical problem-solving abilities seem to have deteriorated over the last two years. Maybe I should practice Leetcode problems more regularly.