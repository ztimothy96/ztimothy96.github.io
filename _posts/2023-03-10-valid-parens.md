---
layout: post
title: "Longest valid parentheses"
subtitle: "Leetcode 32"
thumbnail-img: /assets/img/algorithms.png
share-img: /assets/img/algorithms.png
tags: [algorithms]
---

More Leetcode practice; I seem to gravitate towards hard-level questions these days. Anyway, here's number 32.

## Problem statement

> Given a string containing just the characters "(" and ")", return the length of the longest valid (well-formed) parentheses 
substring.

Sometimes people who don't like learning about algorithms will complain that Leetcode puzzles are pointless. And sure, some of them can sound pretty artificial, but this one certainly isn't. You probably use similar parenthesis-matching algorithms every day! VS Code, the interface that I'm using to write this post, might use them to color-code matching brackets in text, compilers may use them to check for syntax errors in computer programs, and so on...

## Intuition
As usual, we start by playing around with toy examples and clarifying the problem. For example, consider the string $$s = ()(()))()($$. The longest valid substring has length $$6$$, because $$s$$ contains two groups of valid parentheses $$()(())$$ and $$()$$, separated by some parentheses without a match. Note that substrings have to consist of adjacent characters; you can't skip around.

Now that we understand what's going on, we can find a simple brute-force solution. Naively, we can check every possible substring and return the longest valid one. Since there are $$O(n^2)$$ substrings and it takes $$O(n)$$ time to check each one, this approach takes $$O(n^3)$$ time. It's slow because it wastefully duplicates work; in the example above, if we know that $$()$$ and $$(())$$ are valid substrings, then we instantly know that $$()(())$$ is also valid without checking it separately. So it should be possible to solve the problem much more quickly.

While solving our toy example mentally, we probably didn't go through and check every possible substring. Instead, we performed some kind of linear scan from left to right. As we see a closed parenthesis, we go back and match it with an open one. Then the longest valid substring including that pair would consist of everything between it, plus any adjacent matching pairs preceding it. As we keep scanning, if at any point we can't append any more pairs (such as when we hit the closed parenthesis at position 7), we note the length of the longest group we found so far. Then we restart the process with the remaining substring.

## Solution
Let's describe this intuition a little more precisely. To this end, we'll define a few helpful concepts:
* The *level* $$k$$ of a string is the set of its parentheses with $$k$$ preceding open parentheses.
* A level is *incomplete* if we can still append more matching pairs of parentheses to it. Otherwise, it is *complete.*

We start at level 0, having seen the empty string. We'll scan through the string, and at each iteration $$i$$, we'll track the number of matched parentheses at each incompleted level of the substring $$s_i$$ seen so far. If we see "(" we'll add a new level, and if we see ")" we'll close out the pair and add everything between them to the count of the previous level. Since we're processing the parentheses in LIFO order, we should store these counts in a stack $$S_i$$, which can push and pop in $$O(1)$$ time. The algorithm therefore uses $$O(n)$$ time and $$O(n)$$ space. 

To make the above clear, let's trace through what the level counts would look like in our toy example.
- $$s_0=``"$$, $$S_0 = [0]$$. 
- $$s_1=``("$$, $$S_1 = [0, 0]$$. 
- $$s_2=``()"$$, $$S_2 = [2]$$. 
- $$s_3=``()("$$, $$S_3 = [2, 0]$$. 
- $$s_4=``()(("$$, $$S_4 = [2, 0, 0]$$.
- $$s_5=``()(()"$$, $$S_5 = [2, 2]$$.
- $$s_6=``()(())"$$, $$S_6 = [6]$$.
- $$s_7=``()(()))"$$, $$S_7 = [0]$$. Here we note that we found a valid substring of length 6 and restarted the count.

Here's some simple Python code that implements this logic (plus a few bugfixes):

```python
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        ans = 0
        stack = [0]
        for paren in s:
            if paren == '(':
                stack.append(0)
            else:
                curr = stack.pop()
                if stack:
                    prev = stack.pop()
                    stack.append(prev + curr + 2)
                else:
                    ans = max(curr, ans)
                    stack.append(0)
        ans = max(ans, max(stack))
        return ans
```

As expected, it performs fairly well, reaching the 97-th and 89-th percentiles for time and space respectively.