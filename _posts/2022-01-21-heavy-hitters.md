---
layout: post
title: "List decoding"
subtitle: "Algebraic coding theory, part 8"
thumbnail-img: /assets/img/message.png
share-img: /assets/img/message.png
tags: [math, coding theory, algebra]
---
This is part of a series in which I work through Prof. Mary Wootters' Youtube [lectures](https://www.youtube.com/watch?v=dq3IhKxua00&list=PLkvhuSoxwjI_UudECvFYArvG0cLbFlzSr&index=42) on algebraic coding theory. This post is about using list recovery techniques to solve the heavy hitters problem from streaming algorithms.

## Heavy hitters
Imagine Alice is running an online shop, and people start buying all sorts of items from her service. She sells items from a universe $$\mathcal{U}$$ of $$N$$ possible items, where $$N$$ is very large. Every minute or so, somebody buys another item $$x_i$$. In order to supply her inventory, she would like to know which of the items are most popular. Unfortunately, there are so many purchases every day that she simply can't store the entire stream of data. How can she still find the most "heavily hit" items?

This is one possible story for why we might want to solve the heavy hitters problem.

**Problem.** Given a stream $$x_1, ..., x_m \in \mathcal{U}$$, where $$\mathcal{U}$$ is a set of size $$N$$, output a list $$\{x \in \mathcal{U}: x \text{ appeared } \geq \epsilon m \text{ times in the stream } \}$$.

Naively, we could store the entire stream, or instead store counts for each item in the universe. However, both of these approaches require too much memory. In fact, if we want to solve the problem exactly, we need to use $$\Omega(N)$$ space, which is really bad.

So let's change the problem: instead of finding the heavy hitters all the time, let's find approximate probable heavy hitters. We might return more items than just the heavy hitters, and with some small probability we might fail to find all of the heavy hitters. However, as long as we usually find a small list that includes the most important items, we've still learned something useful from our data stream.

**Problem.** Given a stream $$x_1, ..., x_m$$, output a set $$S \subseteq \mathcal{U}$$ such that with high probability,
- $$S$$ contains all heavy hitters $$x$$ which appear in the stream $$\epsilon m$$ times, and
- $$\vert S \vert \leq 2 / \epsilon$$.

It turns out that relaxing the problem allows us to save considerable space. In fact, we can perform this task using $$O(\log (N) \log (m) / \epsilon)$$ memory, using a data structure known as *count-min sketch* (CMS).

## Count-min sketch

The data structure consists of $$T$$ arrays $$(A_i)$$, where $$T = \log N$$. Each of the arrays has length $$4/\epsilon$$. Moreover, for each array $$A_i$$ we have a hash function $$h_i: \mathcal{U} \to [4 / \epsilon]$$, going from our universe of items to a position in the array.

**Update.** Upon seeing $$x \in \mathcal{U}$$, increment the count in $$A_i[h_i(x)]$$ for each array $$i$$.

If $$x$$ is a heavy hitter, then the positions $$A_i[h_i(x)]$$ where it lands will fill up. There may be other items that also hash to the same bucket, but it's unlikely that the other heavy hitters will collide with $$x$$ in all of the arrays. Thus, there should be at least one array which counts only $$x$$ and some low-frequency items, and that should give us a good estimate for how many times $$x$$ appears in the stream.

**Query.** To estimate the number of times some $$x$$ has appeared, take the minimum count across all the arrays, $$\min \{A_i[h_i(x)] : i \in [T] \}$$. Return the items with the greatest estimated count.

This algorithm finds all the heavy hitters with high probability when we take $$2/\epsilon$$ items (details omitted). This is great, because we've managed to save a lot of space, reducing it from $$N$$ to $$\log N$$. However, the algorithm is rather slow, since finding the heavy hitters still involves looping through all $$N$$ items in the universe and finding those with the highest estimated frequencies. Next, we'll see a way to speed up runtime using list recovery.

## List recovery to the rescue
We'll describe now how to speed up heavy-hitter queries using Reed-Solomon (RS) codes. (Check out lecture notes for even more applications of list recovery in cryptography, group testing, and compressed sensing.)

First, let's think about the connection between heavy hitters and error-correcting codes. In heavy hitters, we're trying to quickly recover heavy-hitting items in a stream. Meanwhile, in list recovery, we're trying to recover possible codewords given a list of possible letters in each position. This comparison immediately suggests that we should encode the items as codewords, so that we can list recover them. Since we want to recover high-frequency codewords, we should probably create the lists of possible letters by estimating frequencies somehow.

Actually, there are two methods we can look at so far...

### RS hashes
One way is to *replace the random hash functions with RS codes.* Indeed, let $$k = \epsilon n / 10$$, $$n=q$$, and consider the code $$RS(n, k)$$ with evaluation points $$\alpha$$. By the Guruswami-Sudan algorithm, this code is $$(p=0, \ell=1/\epsilon, L = O(1/\epsilon))$$-list recoverable in $$\text{poly}(n)$$ time. Let's represent the items as messages to this code, which are polynomials $$f$$ of degree $$< k$$. In particular, $$n = O(\log \vert U \vert / \epsilon)$$.

**Update.** Just like in CMS, every time we see a new item $$f$$ from the stream, we compute its hashes $$h_i(f)$$ and increment the counts in those buckets. But instead of a random hash, we're going to replace it with the RS code. Each $$h_i(f) = f(\alpha_i)$$, which outputs a letter $$\sigma$$ denoting a bucket in array $$i$$.

**Query.** Instead of naively looping through all the items in $$U$$, we can apply list recovery instead, to get a much faster query! As hinted at before, we create the recovery list for coordinate $$i$$ by picking the highest-frequency buckets $$\sigma$$ from array $$i$$. Let $$S_i = \{\sigma: A_i[\sigma] > \epsilon m$$. Then apply the list-recovery algorithm to these lists. Since $$S_i$$ contains at most $$1/\epsilon$$ items, we return a list of $$O(1/\epsilon)$$ items. Since we are looping over all the buckets instead of the universe, we have reduced the runtime to $$\text{poly}(n)$$.

As a bonus, the algorithm is now deterministic; we're guaranteed to find all of the heavy hitters all of the time!

### Little CMS structures
Another idea involves using many smaller copies of the CMS data structure. Instead of working on the universe $$U$$, initialize $$n$$ CMS structures for the small universe $$\mathbb{F}_n$$. Since the data structures are small, we can run exhaustive queries on them in $$\text{poly}(n)$$ time. We can now use list recovery to combine all of these structures and solve heavy hitters on $$U$$.

**Update.** Once again, we'll treat our items as polynomials $$f$$ and encode them using the RS code. This gives us a codeword $$f(\alpha_1), ..., f(\alpha_n)$$. Then we're going to store each $$f(\alpha_i)$$ into the $$i$$-th CMS structure and perform an update there.

**Query.** Going backward, we query each of the little CMS structures to return a list $$S_i$$ of heavy hitters for each coordinate. Then we can use these lists to perform list recovery on the whole codeword $$f(\alpha)$$. Once again, this will give us a fast query algorithm.

It's worth noting that, in this second approach, the small CMS structures are allowed to fail, so the query still has some failure probability. However, I think if you check the details, then the query runtime should be faster than that of the previous method. Both of the methods have slightly higher update time and space requirements than the naive CMS method, but have much faster queries.

There are even fancier algorithms for solving heavy hitters, but the above data structures still provide simple and effective examples of how list recovery can be applied to other problems.
