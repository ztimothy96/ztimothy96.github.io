---
layout: post
title: "Edit distance"
subtitle: "Leetcode 72"
thumbnail-img: /assets/img/algorithms.png
share-img: /assets/img/algorithms.png
tags: [algorithms]
---

I've got to keep my logical problem-solving skills sharp somehow, so back to Leetcode we go. I never really understood why people feel the need to "grind" hundreds of problems until they memorize the solutions, as it's quite possible to solve most of them by applying a few basic principles. For example, Leetcode 72 (edit distance) offers a classic example of dynamic programming and related divide-and-conquer strategies.

## Problem statement

> Given two strings *word1* and *word2*, return the minimum number of operations required to convert *word1* to *word2*. You have the following three operations permitted on a word:
- Insert a character
- Delete a character
- Replace a character

## Intuition
As usual, we can get a feel for the problem by looking at toy examples, so suppose *word1* is "a" and *word2* is "ab". Let's start by trying to turn a complex problem into several simpler ones (recursion), transforming *word1* into *word2* one letter at a time. Since strings have an order, let's start at the end and work back to the beginning. We know that the last two letters are "a" and "b" respectively, so we somehow need to get them to match. There are only a few options; we can either 
- add "b" to the end of *word1*, 
- delete "a" hoping that there will be a "b" earlier, or
- replace "a" with "b". 

Having gotten rid of one of the last letters, we can proceed to match the remaining substrings. Repeating this process, we can eventually reduce one of the words to an empty string, and then the problem will be trivial to solve. If we know how to solve the smaller problems on the substrings, then we can solve the original problem by simply picking the best operation to use (insert, delete, or replace). In our toy example, it is best to append "b" to *word1* and then match the remaining substrings, "a" and "a". The total edit distance is therefore 1.

## Solution
We can write up the above strategy precisely using dynamic programming. Let $$n, m$$ be the lengths of our strings. Further, let $$OPT(i, j)$$ be the minimum number of operations required to edit the first $$i$$ letters of *word1* into the first $$j$$ letters of *word2*. By the discussion above,

$$OPT(i, j) = \max \{OPT(i-1, j), OPT(i, j-1), OPT(i-1, j-1) + \mathbb{1} \{ word1[i] \neq word2[j] \} \}.$$

(The last case says that it doesn't cost us any edits to match the last two letters if they are already the same.) We have the base case $$OPT(0, j) = j, OPT(i, 0) = i$$ for any $$i, j$$. Then we can simply iterate over all $$i, j$$ until they reach $$n, m$$, solving our original problem.

Now for the performance analysis. Since we solve $$O(nm)$$ subproblems each in $$O(1)$$ time, the overall runtime is $$O(nm)$$. We might initially think that we should also need $$O(nm)$$ space to store the array of solutions for each subproblem. However, notice that to compute $$OPT(i, j)$$, we only need to look up the solutions to subproblems in the previous row and column. Therefore, we only need to store 2 rows, and we can get away with using only $$O(\min\{n, m\})$$ memory. My naive implementation of the above program lands in the 50-th and 95-th percentiles for runtime and space, respectively. 

## P.S.
If I remember correctly from graduate school, there is a whole branch of algorithms research about whether it's possible to find edit distance substantially faster than this simple DP approach, as an $$O(n+m)$$-time algorithm would be incredibly useful for gene sequencing. Alas, so far nobody has succeeded, so now people spend their time trying to prove that it's impossible. Meanwhile, biologists have resorted to heuristics like [BLAST](https://blast.ncbi.nlm.nih.gov/Blast.cgi).