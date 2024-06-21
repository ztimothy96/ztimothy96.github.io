---
layout: post
title: "Designing a URL shortener"
subtitle: "System design interviews, chapter 9"
thumbnail-img: /assets/img/system-design/swe.png
share-img: /assets/img/system-design/swe.png
tags: [software engineering]
---
I'm re-reading *System Design Interview: An Insider's Guide* by Alex Xu. Here's Chapter 9 on how to design a web crawler.

## Understand the problem
This is perhaps the first real example of system design in the book. A web crawler essentially performs a graph search on the Internet. It starts from a couple of URLs, downloads the pages, extracts links to other URLs from those pages, and repeats the process, exploring more and more of the online world.

**Functional requirements**:
- What is the purpose of the crawler? e.g. will we use it for web page indexing like Google PageRank?
- How many web pages to collect?
- What content type to store? Just HTML, or also images and PDF?
- How long do we need to store the pages?
- How to handle pages with duplicate content?

**Non-functional requirements**:
- High scalability. We're crawling the Internet, which is gigantic.
- High fault tolerance. Should be able to handle malicious or malformed web pages, unresponsive servers, etc.
- Extensibility. We should be able to add new types of collected content without changing the whole architecture much.
- Politeness. We don't want to spam the same website with frequent requests, as that could create a denial-of-service (DoS) attack.

## High-level design

The workflow is pretty straightforward. We have to perform the following steps:
- Frontier: Store the URLs that haven't yet been downloaded in some queue.
- Downloader: download some HTML from some URL in the queue.
- Content seen?: figure out what's in the HTML.
- Content storage: if we haven't seen this page before, store it in a database.
- URL extractor: extract the new URLs from the HTML we just parsed.
- URL filter: don't put all of the new URLs into the queue. Sometimes there may be sketchy websites we want to avoid.
- URL seen?: if we haven't seen it and it passed all our filters, append it to the queue.

We can simply take each of these tasks and make a component of our system to focus on that task. The system architecture reflects the algorithm we have in mind, and it assigns each step to an independent service so that we can separate concerns. Now we can focus on a few of the components and design them in greater detail.

![9-2](/assets/img/system-design/9-2.png){: .mx-auto.d-block :}


## Detailed design

### URL Frontier
The URL frontier is the queue where we store all the URLs that have not yet been processed. Here we can choose which order to make requests so that we can better satisfy our non-functional requirements.

- Priority. Some pages may be more important than others, so it would make sense to download them first and extract all their associated links. (Example: the Wikipedia homepage is probably more important than an obscure Wiki article.) Instead of trying to sort all the requests coming in a stream, it would be easier for a prioritizer component to distribute them into three queues of "High," "Medium," and "Low" priority.

![9-7](/assets/img/system-design/9-7.png){: .mx-auto.d-block :}


- Politeness. To avoid spamming a server repeatedly, we can try to enforce a wait between making requests to the same host (e.g. wikipedia.com, apple.com...). To this end, we would have to put URLs from the same host into the same queue. That's why we can create several different queues, and create a queue router with a lookup table from hostname to queue. Upon receiving a URL, the router will read its hostname and look up the right queue to put it into. The queue will then enforce delays before popping its next item, which is then fed to a bunch of worker threads that perform the download.

![9-6](/assets/img/system-design/9-6.png){: .mx-auto.d-block :}

- Storage. We'll get too many URLs to store in memory, but we don't want to store everything on the disk either. Otherwise, retrieving items from the queue will take forever. Instead, use a hybrid approach where we store some of the items in a buffer, and we only retrieve from disk when the buffer has been depleted. This way we make far fewer fetches from disk space.

### Downloader

- Distribution: split the download tasks between several servers in a cluster. Well, this is obvious... use something like consistent hashing, perhaps.
- Cache DNS resolver. In a naive approach, a downloader would have to take the domain name and request to find the IP address from a DNS server. It would be great to cache the results so that we don't have to repeatedly ping those servers.
- Locality. If we're distributing the downloads, it would make sense to assign downloads to servers that are closest geographically to the website host servers. Think of DNS.
- Short timeout. If the host server isn't responding, the downloader should give up and try to download something else. Don't get stuck waiting for a response.


## Look back

This was a pretty helpful example of how to think through system design. We wrote a simple algorithm for a single-server web crawler, and we implemented it in a distributed system by assigning each step of the algorithm to an independent component. We then went into each component and made some lower-level design choices to fulfill non-functional requirements. Not the most mind-blowing concept ever, but hey, system design is not about inventing new wheels. It's about figuring out what existing technologies and patterns best solve the practical problem in front of us.

**Exercise.** If we wanted to also download PDFs and image files from the HTML, where should that extension go in our architecture?
