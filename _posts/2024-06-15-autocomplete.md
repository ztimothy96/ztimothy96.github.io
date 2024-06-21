---
layout: post
title: "Designing an autocomplete system"
subtitle: "System design interviews, chapter 13"
thumbnail-img: /assets/img/system-design/swe.png
share-img: /assets/img/system-design/swe.png
tags: [software engineering]
---
I'm re-reading *System Design Interview: An Insider's Guide* by Alex Xu. Here's Chapter 13 on how to design an autocomplete system.

## Understand the problem
When we type a search into a Google search bar, we might notice that Google offers a few suggestions about the most common searches starting with the string we entered. That's very convenient! How could we build such a feature?

**Functional requirements**:
- Given a search string, generate 5-6 autocomplete suggestions starting with that input string.
- The input should be short. Past a certain point (say 50 characters), the search becomes too niche, and we don't care about autocomplete anymore.

**Non-functional requirements**:
- Fast response time. This needs to be *blazing* fast. We don't want to wait forever as our search engine lags trying to generate autocomplete suggestions.
- Highly scalable. Assume we have tens of millions of daily active users (DAU).
- Highly available.

## High-level design
As a starting point, we can do the dumbest thing imaginable: collect counts on how many times each search people perform and store them all in a giant key-value store. Then, every time someone starts up a new search, query the database for all strings starting with their input and find the top 5 most frequent entries.

This design is slow as hell, and all our servers would catch fire and burn to the ground. So we should think of a way to make it faster.

## Detailed design
Use a trie. No, seriously: that's the entire solution. Tries are a data structure invented expressly for speeding up retrieval tasks on strings.

If enough users search for enough different strings, then the trie may become so large that we can't store it in a single server, so we'd have to partition and replicate it, using any standard technique of choice. That's fine; we always want to replicate our data so that it becomes more fault-tolerant anyway. Then we can add some more tricks like client-side caching to reduce redundant requests and speed up our service some more. But these are ideas we've already seen before.

The author spends a good amount of time explaining the basics of how tries work. I am confused about why this chapter was included in a system design book. It is, after all, about algorithms and data structures, straight out of a Leetcode problem, and has very little to do with system design.

## Look back

Algorithms and data structures are sometimes insanely useful for building real-world search engines. Who would have guessed? Not the kind of people who like to complain about grinding Leetcode, probably.
