---
layout: post
title: "Designing a web crawler"
subtitle: "System design interviews, chapter 9"
thumbnail-img: /assets/img/system-design/swe.png
share-img: /assets/img/system-design/swe.png
tags: [software engineering]
---
I'm re-reading *System Design Interview: An Insider's Guide* by Alex Xu. Here's Chapter 9 on how to design a web crawler. This is perhaps the first real example of system design in the book.

## Understand the problem
A web crawler essentially performs a graph search on the Internet. It starts from a couple of URLs, downloads the pages, extracts links to other URLs from those pages, and repeats the process, exploring more and more of the online world.

**Functional requirements**:
- What is the purpose of the crawler? e.g. will we use it for web page indexing like Google PageRank?
- How many web pages to collect?
- What content type to store? Just HTML, or also images and PDF?
- How long do we need to store the pages?
- How to handle pages with duplicate content?

**Non-functional requirements**:
- *High scalability.* We're crawling the Internet, which is gigantic.
- *High fault tolerance.* Should be able to handle malicious or malformed web pages, unresponsive servers, etc.
- *Extensibility.* We should be able to add new types of collected content without changing the whole architecture much.
- *Politeness.* We don't want to spam the same website with frequent requests, as that could create a denial-of-service (DoS) attack.

## High-level design

Starting from a vanilla architecture, we can modify the system to meet the functional requirements. In this case, there are no users, so we can remove them from the design. Whereas in the previous chapter, our API servers simply had to read and write from a database, here their task is more complicated. We can break down the graph search into several steps:

- *Frontier*: store the URLs that haven't yet been downloaded in some queue.
- *Downloader*: download some HTML from some URL in the queue.
- *Content seen?*: figure out what's in the HTML.
- *Content storage*: if we haven't seen this page before, store it in a database.
- *URL extractor*: extract the new URLs from the HTML we just parsed.
- *URL filter*: don't put all of the new URLs into the queue. Sometimes there may be sketchy websites we want to avoid.
- *URL seen?*: if we haven't seen it and it passed all our filters, append it to the queue.

We can divide up our API servers into components, each of which performs a single step in this algorithm. We have also split the databases to store different kinds of data. By separating concerns, we have made the system architecture easy to understand and maintain. (If you're a manager at a company, it's now easier to delegate each of these components to a different engineering team as well.)

![9-2](/assets/img/system-design/9-2.png){: .mx-auto.d-block :}

## Detailed design
Now we can focus on a few of the components and design them in greater detail. The book zooms in on the URL frontier and downloader components, although it's certainly possible to delve into other parts of the system instead.

### URL Frontier
The URL frontier is the component that stores all the URLs that have not yet been processed. We should probably use a queue, as that's the data structure designed for FIFO data streams. Here we can choose which order to make requests so that we can better satisfy our non-functional requirements.

- *Priority*. Some pages may be more important than others, so it would make sense to download them first and extract all their associated links. (Example: the Wikipedia homepage is probably more important than an obscure Wiki article.) Instead of trying to sort all the requests coming in a stream, it would be easier for a prioritizer component to distribute them into three queues of "High," "Medium," and "Low" priority.

![9-7](/assets/img/system-design/9-7.png){: .mx-auto.d-block :}

- *Politeness*. To avoid spamming a server repeatedly, we can try to enforce a wait between making requests to the same host (e.g. wikipedia.com, apple.com...). To this end, we would have to put URLs from the same host into the same queue. That's why we can create several different queues, and create a queue router with a lookup table from hostname to queue. Upon receiving a URL, the router will read its hostname and look up the right queue to put it into. The queue will then enforce delays before popping its next item, which is then fed to a bunch of worker threads that perform the download.

![9-6](/assets/img/system-design/9-6.png){: .mx-auto.d-block :}

- *Storage*. We'll get too many URLs to store in memory, but we don't want to store everything on the disk either. Otherwise, retrieving items from the queue will take forever. Instead, use a hybrid approach where we store some of the items in a buffer, and we only retrieve from disk when the buffer has been depleted. This way we make far fewer fetches from disk space.

### Downloader

The downloader is the component that takes in URLs and retrieves the HTML from the web. We can make some optimizations to improve the latency here.

- *Distribution*: split the download tasks between several servers in a cluster. Use something like consistent hashing, perhaps.
- *Cache DNS resolver*: in a naive approach, a downloader would have to take the domain name and request to find the IP address from a DNS server. It would be great to cache the results so that we don't have to repeatedly ping those servers.
- *Locality*: if we're distributing the downloads, it would make sense to assign downloads to servers that are closest geographically to the website host servers. Think of DNS.
- *Short timeout*: if the host server isn't responding, the downloader should give up and try to download something else. Don't get stuck waiting for a response.


## Look back

This was a pretty helpful example of how to think through system design. We wrote a simple algorithm for a single-server web crawler, and we implemented it in a distributed system by assigning each step of the algorithm to an independent component. We then went into each component and made some lower-level design choices to fulfill non-functional requirements. Some key takeaways from this design session:

- *Separate concerns.* If the API servers need to perform several steps in a graph search algorithm, split those steps into different components. If the databases need to store multiple kinds of data, split them into multiple databases. This makes the system easy to understand and modify.
- *Queues are glue.* Whenever we need to send asynchronous messages or tasks from one component to another, we should probably use a queue. Queues not only help to separate concerns (as now neither component has to worry about how to distribute messages to the other), but they also allow us to customize the order and perform load balancing. All aspects of message transport are handled inside the queue itself.

Not the most mind-blowing concept ever, but hey, system design is not about inventing new wheels. It's about figuring out what existing technologies and patterns best solve the practical problem in front of us.

**Exercise.** If we wanted to also download PDFs and image files from the HTML, where should that extension go in our architecture?
