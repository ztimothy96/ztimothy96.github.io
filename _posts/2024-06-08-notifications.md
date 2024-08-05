---
layout: post
title: "Designing a notification system"
subtitle: "System design interviews, chapter 10"
thumbnail-img: /assets/img/system-design/swe.png
share-img: /assets/img/system-design/swe.png
tags: [software engineering]
---
I'm re-reading *System Design Interview: An Insider's Guide* by Alex Xu. Here's Chapter 10 on how to design a notification system.

## Understand the problem
Create a system that can receive requests from apps to send notification messages to their users.

**Functional requirements**:
- Messages can be sent via push notification, mobile text, or email.
- Notifications can be triggered via either requests from apps or from the server itself.
- The notification system should work on multiple platforms, phones and laptops alike.
- Users can select to opt out of messages.

**Non-functional requirements**:
- *High scalability.* Think about tens of millions of messages per day.
- *Moderate performance.* Ideally, the notifications should arrive in real time, but slight delays are acceptable.

## High-level design

Different third-party services are used to send messages on different platforms. For instance, we should use Apple Push Notification Service (APNS) to send notifications on iOS; Firebase Cloud Messaging (FCM) for Android phones; SMS services for SMS; and MailChimp for emails. We have to figure out how to collect data about user platforms and send notifications to those platforms.

Collecting user data is pretty easy: we can ask them when they sign up for the app, then write to databases using a vanilla architecture. We can think about what fields to put under users and devices and how to join the tables, but these details are too low-level and out of scope for this write-up.

![10-7](/assets/img/system-design/10-7.png){: .mx-auto.d-block :}

Sending the notifications is more interesting; we'll have to adapt the vanilla architecture a bit. The users are services that need to send notifications. They'll call our notification API server, which looks up user platforms and forwards the requests to the appropriate third-party service, which then sends the actual message to the platforms.

![10-9](/assets/img/system-design/10-9.png){: .mx-auto.d-block :}

Since it would be a terrible idea to put the entire notification service on a single server, we should distribute the task. So we'll turn our notification server into a distributed cluster, and we'll move all the user data into a database so everybody can access it. We can also put notification requests to third parties in a message queue so that they don't disappear if a notification server dies.

![10-10](/assets/img/system-design/10-10.png){: .mx-auto.d-block :}


## Detailed design

Xu now suggests several ideas for improving the initial vanilla design.

- *Reliability*: notifications cannot be lost. We can keep a log of notifications that allow us to retry sending when a message fails. Since a distributed system may send a notification more than once, we should also implement a de-duplication method. A simple one might assign every notification an ID, and when the notification arrives, check if the device has already seen the ID. If it hasn't, display the notice.
- *Notification templates*: we might want to use templates to generate messages instead of having the service send the entire thing when making a request. This could reduce latency.
- *Notification setting*: it allows the users to opt in or out of different kinds of notifications. This can be stored in a relational database, with fields: user, channel, opt-in.
- *Rate limiting*: we might also want to rate limit how many notifications a user can receive to avoid blowing up their phone.
- *Monitoring*: keep track of how many notifications are queued, to make sure that we have enough workers to process all of them. (Unlike in Factorio, a software engineer can't easily visually inspect our transport belts.)
- *Analytics*: we may also want to collect data on open rate and click rate so we can figure out how to farm more users for our app. Yay, technological surveillance.

We can put all of these cute little improvements back into our design. Now it's getting kind of complicated.

![10-14](/assets/img/system-design/10-14.png){: .mx-auto.d-block :}


## Look back

That was a high-level look at designing a notification system. We didn't talk much about how to implement things like the retry mechanism, but I think that's okay. The lesson here is about thinking of all the things we might want from our system to improve the user experience, and then figuring out where to put them in the architecture.

Once again, we can note some general takeaways from this design session:
- *Queues are glue.* We used them to send messages from notification API servers to third-party servers. Need to process some asynchronous tasks? Use a queue.
- *Separate your workflows.* In this example, we had two main workflows: storing user data and sending notifications. We designed different architectures for each task. One of them ended up being a lot more complicated than the other.
- *Peanut butter jelly time.* I feel that some components often like to be paired together, like peanut butter and jelly, to improve performance. For instance: databases with a cache or CDN, API servers with a rate limiter, message queues with a logging and deduplication system, etc. And can you ever go wrong with adding monitoring or analytics services?
