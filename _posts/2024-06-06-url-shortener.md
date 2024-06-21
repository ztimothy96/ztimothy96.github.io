---
layout: post
title: "Designing a URL shortener"
subtitle: "System design interviews, chapter 8"
thumbnail-img: /assets/img/system-design/swe.png
share-img: /assets/img/system-design/swe.png
tags: [software engineering]
---
I'm re-reading *System Design Interview: An Insider's Guide* by Alex Xu. Here's Chapter 8 on how to design a URL shortener.

## Understand the problem
Ok, let's design a service similar to TinyURL. It will take in URLs and map them to a shorter alias.

**Functional requirements**:
- URL shortening: given a long URL, return a shorter URL.
- URL redirection: given a short URL, redirect the user to the original page.
- Assume for simplicity that these short URLs are immutable; they won't be updated or deleted.

**Non-functional requirements**:
- High availability.
- High fault tolerance.
- High scalability.

## High-level design

As usual, we will allow clients to send requests through a REST API such as HTTP. URL shortening can be a POST request, while redirection can be a GET request. When replying to the client, we can redirect them with either a 301 or 302 HTTP code; a 301 code means that the webpage has been moved permanently, so the client can cache the tiny URL and not have to bother our service again. However, a 302 code would say that the webpage has only been moved temporarily, so the client would make a new request every time they want to see the page. Here 302 codes are better if we don't mind the extra traffic and want to collect analytics for our totally-not-evil corporation. Isn't this exciting?

The workflow is pretty simple: the user makes some requests, we have a bunch of API servers to accept those requests, and then they'll read or write URLs to and from some database. Since this service is read-heavy, we'd likely add a cache to store the most frequently requested websites. Nothing too surprising here.

![8-8](/assets/img/system-design/8-8.png){: .mx-auto.d-block :}


### Hashing

Okay, so now we have to figure out the actual details. Given a long URL, how do we hash it to a short URL and store the pair in a database? This chapter covers two hashing solutions: hash-collision resolution, and base 62 hashes.

Hash-collision resolution is exactly what it sounds like; we apply a standard hash function (like SHA) to the URL. However, the hashed output can be longer than we want, so we truncate the hash to just the first say 7 characters. But this might occasionally result in a hash collision, so we keep appending pre-defined characters to our hash until we get a unique URL. We can quickly check for collisions in a large set of hashes using a Bloom filter. Well, that's pretty straightforward; inventing Bloom filters is probably a more interesting problem, but it's beyond the scope of this book...

Base 62 is arguably even simpler. Just generate a numeric unique ID (as described in the previous chapter) and convert it to base 62. We picked base 62 because if we use all alphanumeric characters, capitalization included, there are 26+26+10=62 such characters available for encoding. The URLs will all be unique because of the unique ID generator. However, if the generator simply increments IDs, that could lead to a security leak where people can guess the next URL.

## Look back

After going into the details on how to design a hash function, we could go back and talk about other possible improvements to the system. This could involve adding a rate limiter, scaling web servers, scaling the databases, etc. This is not complicated; we can scale our web servers for free because they don't need to know anything about the client state, and we can scale our databases through standard partition and replication methods covered in an earlier chapter.

I'm feeling somewhat disappointed while reading this book so far. Hash functions are cool and all, but this chapter doesn't feel focused on system design. Tell me something new.
