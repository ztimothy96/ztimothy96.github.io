---
layout: post
title: "Beyond single deletion correcting codes"
thumbnail-img: /assets/img/message.png
share-img: /assets/img/message.png
tags: [math, coding theory, algebra]
---

Now that I'm feeling happier elsewhere in life, I can get back to learning about cool things like error-correcting codes! Today, I'm grokking "Beyond Single Deletion Correcting Codes: Substitutions and Transpositions", from APROX-RANDOM 2022. You can find the manuscript on [Arxiv](https://arxiv.org/abs/2112.09971) and the conference talk on YouTube. I probably won't read in detail, as I'm just trying to get an overview of what questions people are investigating these days. As of June 2023, the paper has been cited 8 times, and it might be worth tracing the more recent work.

In other news, it's kind of fun to notice that other people are citing my undergraduate NeurIPS paper.

## Multiple error models
There's an old saying attributed to Dijkstra: "Computer science is no more about computers than astronomy is about telescopes." Very true! Instead of using conventional computers, we may someday try to store data on strands of DNA due to its extremely high information density. However, DNA can be corrupted over time by mutations such as substitutions, insertions or deletions, and transpositions. If we want to make DNA databases, we need to figure out how to perform error correction on this new medium. Let's take a look at each type of error in turn.

Substitution errors already happen on conventional computers, so we understand them well; classical methods like Reed-Solomon codes can correct them perfectly fine for most practical purposes. However, insertions and deletions pose problems unique to DNA storage. Recently, there's been a flurry of work on deletion codes, and scientists have discovered that they're quite difficult to correct. The main trouble is that we lose synchronization; we don't know which letters in our corrupted sequence correspond to which letters of the original sequence. Transpositions pose similar problems because we can view them as combinations of insertions and deletions.

While computer scientists have made lots of progress in tackling these errors individually, they haven't looked so much at the *intersection* between them. When building a real DNA-based storage system, we'd like to devise codes that can handle all kinds of errors at once. This research area is still in its infancy, so nobody knows much about the topic; it's it's a mysterious dark void, waiting to be explored. And in my opinion, that's the most exciting part of science!

This paper focuses on trying to tackle two kinds of single-symbol errors at once. In particular, they give algorithms that can correct the following kinds of errors:
- one deletion or substitution on a $$q$$-ary alphabet in $$\log n + O_q(\log \log n)$$ time,
- one deletion or adjacent transposition on bits in $$\log n + O(\log \log n)$$ time,
- one deletion and one substitution on bits in $$ 4 \log n + O(\log \log n)$$ time.

In the APPROX talk, paper author Wu talks about the first result for correcting deletions and substitutions. 

## VT Sketch 
Their algorithm takes the idea of a VT sketch, which works on bits, and generalizes it to arbitrary alphabet sizes. VT sketches are old codes that were discovered in the 60's. Given binary message $$x$$, we encode information about it into a single number, the VT sketch: $$f(x) = \sum_{i=1} i \cdot x_i$$. Since the sum ranges up to $$O(n^2)$$, the sketch used $$O(\log n)$$ extra bits. 

VT sketches are important because they can correct single bit-flip and deletion errors. It's pretty obvious how we would correct a bit flip error: if $$x$$ is corrupted to $$y$$ by flipping bit $$e$$, then $$f(y)-f(x) = e(y_i - x_i)$$ gives us the position of the flipped bit. Correcting one deletion is a bit trickier. Suppose we obtain $$y$$ by deleting bit $$d$$; then the remaining bits get shifted back, so $$f(x) - f(y) = dx_d + \sum_{d}^{n-1} y_i$$. We can insert a bit back into $$y$$ and try every possible position until we get one that recovers the original VT sketch value. This algorithm takes $$O(n)$$ time, but do we know that it uniquely recovers the original message?

Suppose we corrupt $$x$$ at two positions, $$d < d'$$, to get strings $$y, y'$$. Furthermore, suppose they yield the same VT sketch, $$f(y) = f(y')$$. Then $$0 = f(y) - f(y') = (f(x) - f(y')) - (f(x) - f(y)) = (d' x_{d'} - d x_{d}) - \sum_{d}^{d'-1} y_i$$. We can now look at all possible cases.

- If $$x_{d'} = x_d = 0$$, then $$\sum_{d}^{d'-1} y_i = 0$$ also. We conclude that $$x_{d'}, x_d$$ are connected by a string of $$0$$'s. In any case, the recovered string $$x$$ is unique.
- If $$x_{d'} = x_d = 1$$, then similarly $$d'-d = \sum_{d}^{d'-1} y_i$$. We similarly conclude that the deleted bits are connected by a string of $$1$$'s. 
- If $$x_{d'} = 1$$ and $$x_d = 0$$, then $$d' = \sum_{d}^{d'-1} y_i$$. This is not possible, corresponding to the fact that we can always distinguish when the deleted bits have different values.
- If $$x_{d'} = 0$$ and $$x_d = 1$$, then $$\sum_{d}^{d'-1} y_i < 0$$. This is also impossible.

Previously, VT sketches had been generalized to higher alphabets which could correct a single insertion or deletion, but these codes did not correct substitutions. Since we now also want to correct substitutions, we need to invent a different method of generalization.

## Ternary sketches and beyond
 
Suppose we now have $$q=3$$ letters in the alphabet. (For DNA storage, we want to set $$q=4$$.) We compute the VT sketch in the same way, $$f(x) = \sum_i i \cdot x_i$$. Again, introducing substitution error at bit $$e$$ gives us sketch difference $$f(y)-f(x) = e(y_i - x_i)$$. Now that there are more than $$2$$ letters, we don't know how much of the difference is due to location $$e$$ or the letter difference $$y_i - x_i$$.

Since we need more information to decode, let's append how many letters of each type there are to the end of $$x$$. That is, $$\#0$$ is the number of $$0$$'s in $$x$$; we'll append $$\#0, \#1, \#2$$ to the VT sketch. This will take $$O(\log n)$$ letters. With that, we can correct substitution errors in the string as well. 

How about deletions? Suppose we have deleted the letter $$d$$; with the added information, we can tell whether the symbol deleted was a $$0$$, $$1$$, or $$2$$, so we can tackle the problem in cases.

If we deleted a $$0$$, then $$f(x)-f(y) = \sum_{d}^{n-1} y_i$$. We can still insert $$0$$ into all positions until we find one that works. A similar strategy works when we have deleted a $$2$$.

However, things get trickier if we have deleted a $$1$$. In this case, we may have multiple choices about where to insert the missing letter. For instance, $$102$$ and $$021$$ yield the same VT sketch. The key point here is that the weight of the inserted $$1$$ grows as we insert it farther in the string, and it offsets the loss from shifting the other numbers backward. So as long as there is a contiguous chunk with mean $$1$$, we'll have choices of where to insert another number.

To work around this issue, the authors propose that we adjust the weights of the VT sketch. Instead of simply multiplying each position by the letter at that spot, we set weights for each of the letters: $$w(0) = 0$$, $$w(1) = 1$$, and $$w(2) = 2 \log n$$. By increasing the weight, we can make sure that there aren't any long contiguous runs with mean $$1$$ so that the codeword can be recovered uniquely. We can similarly generalize this idea to larger alphabet sizes, as in the DNA application. Well, that's my understanding of it anyway; I haven't read the paper in much detail.

Maybe I should get a big-picture view of insdel codes before jumping into the thicket...