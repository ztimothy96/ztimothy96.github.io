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
- High scalability. Think about tens of millions of messages per day.
- Moderate performance. Ideally, the notifications should arrive in real time, but slight delays are acceptable.

## High-level design

Different third-party services are used to send messages on different platforms. For instance, we should use Apple Push Notification Service (APNS) to send notifications on iOS; Firebase Cloud Messaging (FCM) for Android phones; SMS services for SMS; and MailChimp for emails. 

![10-6](/assets/img/system-design/10-6.png){: .mx-auto.d-block :}


To send notifications, we need information about users and their devices. We can collect this when a user signs up for our app. This is pretty standard: users connect via load balancers to a bunch of application servers that write their input to a database. We can think about what fields to put under users and devices and how to join the tables, but these details are too low-level and out of scope for this write-up.

![10-7](/assets/img/system-design/10-7.png){: .mx-auto.d-block :}

High-level architecture for sending the notifications is more interesting. We have a bunch of services that need to send notifications. So they call the notification service, which processes requests and sends them to the appropriate third-party service, which then sends the actual message to the appropriate platform.

![10-9](/assets/img/system-design/10-9.png){: .mx-auto.d-block :}

Of course, it would be a terrible idea to put the entire notification service on a single server. So we should find a way to distribute the task. This requires adding more servers and pushing the database out so everybody can access needed data. We can also put notification requests to third parties in a message queue so that they don't disappear if a notification server dies.

![10-10](/assets/img/system-design/10-10.png){: .mx-auto.d-block :}


## Detailed design

### Reliability
Notifications cannot be lost. Keep a log of notifications that allow us to retry sending when a message fails. A distributed system may send a notification more than once, so implement a de-duplication method. Assign every notification an ID, and when the notification arrives, check if we've seen the ID already. If we haven't, display the notice.

### Other considerations
- Notification templates: we might want to use templates to generate messages instead of having the service send the entire thing when making a request. This could reduce latency...
- Notification setting: it allows the users to opt in or out of different kinds of notifications. This can be stored in a relational database, with fields: user, channel, opt-in.
- Rate limiting: we might also want to rate limit how many notifications a user can receive to avoid blowing up their phone.
- Monitoring: keep track of how many notifications are queued, to make sure that we have enough workers to process all of them. (This is not Factorio, where you can easily visually inspect your conveyor belts.)
- Analytics: we may also want to collect data on open rate and click rate so we can figure out how to farm more users for our app...

We can put all of these cute little improvements back into our design. Now it's getting kind of complicated.

![10-14](/assets/img/system-design/10-14.png){: .mx-auto.d-block :}


## Look back

Hmm, that was a high-level look at designing a notification system. We didn't talk much about how to implement things like the retry mechanism, but I think that's okay. The lesson here is about thinking of all the things we might want from our system to improve the user experience, and then figuring out where to put them in the architecture.
