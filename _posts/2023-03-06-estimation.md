---
layout: post
title: "Indoor positioning with wideband signals"
subtitle: "Wireless networks"
thumbnail-img: /assets/img/router.png
share-img: /assets/img/router.png
tags: [wireless networks]
---

Finally, February is over, and I'm happily back to pursuing my pet projects! Originally, I had planned to spend more time writing orchestral music and reading ML/algorithms hybrid papers, but something came up. Within the next week, I need to get a handle on the basics of [telecommunications](https://en.wikipedia.org/wiki/Portal:Telecommunication), particularly ultra-wideband signals for [real-time location systems](https://en.wikipedia.org/wiki/Real-time_locating_system), or RTLS. I've been interested in the transmission of information for a long time (as reflected by my interests in error-correcting codes, information theory, and dimension-reduction algorithms), so learning about telecommunications seems like a natural next step.

I've constructed the cute little story below by piecing together information from a trip down the Wikipedia rabbit hole. Please take it with a large grain of salt, as I'm completely new to the field. 

## The positioning problem and physical media

Suppose we want to solve the problem of *indoor positioning*: creating a real-time locating system that works indoors. For instance, if we lose an iPhone, how can we recover its position using information from nearby devices? While GPS and satellites work perfectly well outside, buildings can reflect or absorb their signals, leading to serious errors indoors. That is why we'll have to use other techniques like *dead-reckoning*: calculating an object's current position using its previous position and other information such as velocity, acceleration, and elapsed time. Intuitively, that's how we silly humans locate ourselves in an environment; we just start at the initial position and take path integrals. A system that performs dead reckoning is called an *inertial navigation* system because it relies on inertial sensors to detect the object's acceleration. However, measurement errors can snowball over time as we integrate, so we need correction algorithms or alternative methods.

Without satellite imaging, we will have to transmit information through other media. It would be nice if our indoor devices could locate themselves, without needing to build any more fancy tech like satellites. Wireless devices usually communicate via radio waves, using one of the perhaps familiar communication standards below.
- **Bluetooth**: allows simple short-range data transfer, but only supports a limited number of devices.
- **Wifi**: allows users to connect to the Internet. Supports more connections, at a faster and higher range, but consumes more power.
- **Ultra-wideband (UWB)**: it's short-range and low-power like Bluetooth, but fast like Wifi, and can send signals along an extremely wide bandwidth. Note that "bandwidth" here refers to the signal processing concept (width of a band in signal frequency domain), rather than the software engineering concept.

Of these technologies, [UWB](https://en.wikipedia.org/wiki/Ultra-wideband) has properties that sound attractive for RTLS applications. We can use it to ping nearby devices quickly to find our location, without consuming very much power or causing RF interference with other devices. That's why Apple introduced it into commercial cell phones with AirTag in 2019. However, each of these various positioning technologies (BT, WiFi, UWB) each have unique advantages and limitations. So instead of relying on a single method, perhaps we can try to combine each of their signals to predict location more accurately. This idea is called *inertial fusion* because it fuses different signal types to construct an inertial navigation system.

## Localization techniques

Now that we've decided to use radio transmissions in a wireless network, let's think about different techniques we can use to localize nodes within that network. There are two kinds of nodes: those whose locations we know, and those whose we don't.

- *Anchor*: a node whose location is already known. These are known as *gNBs* in the 5G literature, or *base stations* for more traditional wireless networks. 
- *Tag*: a node whose location we want to estimate. Also known as *UE* (user equipment) in the 5G literature.

We can figure out the position of a tag by measuring its angles or distances from several anchors. The process of localizing from angle measurements is called *triangulation*, while that of localizing from time or distance measurements is called *trilateration.* Here are some common approaches to localizing a tag.

- **AOA**: angle-of-arrival. The tag blinks at several anchors, which use beamforming to figure out their angle from the tag. We can then use these angles to perform triangulation. This approach suffers from limited resolution (for physics reasons that I don't fully understand yet; I'm not an electrical engineer yet).
- **Ranging**: this is the process of measuring the distance between two nodes. In simple one-way ranging, Alice sends Bob a signal, and Bob responds with the amount of time it took for him to process it, $$\delta_{reply}$$. This reply travels back to Alice for a round trip time of $$\delta_{round}$$. Then the one-way time of flight is $$TOF = (\delta_{round} - \delta_{reply}) / 2$$. Since there may be noise (due to interference, motion, or measurement error), the pair can repeat this process multiple times and average the results to get more accurate estimates.
- **RTT**: round-trip time. My Internet search hasn't yielded anything concrete, but I suspect we can use RTT to localize tags by ranging against a bunch of anchors and then trilateration. At least, that seems to be the description given by [Prof. Horn](https://people.csail.mit.edu/bkph/ftmrtt) at MIT. 
- **TOA**: time-of-arrival. The tag blinks at a bunch of anchors, which measure the times at which they receive the signal. If we know the time the tag sent the original blink, then we can compute the time it took for the blink to travel to each of the anchors. Then, each anchor produces a circular constraint on the tag position, and we can find the tag at the intersection of all the circles. This method requires the anchors and the tag to all have synchronized clocks.
- **TDOA**: time-difference-of-arrival. Once again, the tag blinks at all the anchors, which measure the time at which they receive the signal. Then the difference between time-of-arrival for each pair of anchors produces a hyperbola constraint on the tag location, and we can find the tag at the intersection point of all hyperbolae. This method requires the anchors to have clocks synchronized with each other, but not with the tag.

### Some radio terminology
I just realized I mentioned beamforming earlier and didn't explain what it was. Well, once again I'm not an electrical engineer by any stretch of the imagination, but I do think it would be helpful to know some basic concepts about radios.

- **Sensor array**: this is a group of sensors working together to detect a signal. Multiple sensors can help to amplify an incoming signal or estimate more of its parameters (such as its direction). The name comes from how the sensors are often arranged in a geometric pattern such as a linear or circular array. 
- **Beamforming**: a technique used by sensor arrays to send and receive signals in a specific direction. The sensors in the array send out signals that interfere constructively in one direction and destructively in others, literally forming a directed beam of radio waves.
- **MIMO**: multiple-input multiple-output. It's a technique for increasing the capacity of a radio link using multiple antennas, originating from mathematical research from the 1970's. Apparently, neural networks can be pretty handy for performing MIMO and estimating a communication channel's behavior; I don't know much about how it works, but might return to this topic later.

## References and further reading
While I had taken some graduate-level classes on information theory, probability, and computational geometry in college, I somehow never learned about wireless networks properly. This seems like a fascinating area with lots of new developments and connections to all of my favorite topics, so of course I'm enthusiastic to learn more about it. Here are some resources:

- [COS463](https://www.cs.princeton.edu/courses/archive/spring19/cos463/ ): a Princeton class on wireless networks, offered jointly by the CS and ECE departments.
- [Indoor Positioning Trends in 5G-Advanced: Challenges and Solution towards Centimeter-level Accuracy](https://arxiv.org/pdf/2209.01183.pdf): a review paper that helped me get up to speed with some of the jargon and localization methods.
- [Localization of Wireless sensor networks: Techniques and Future Trends](https://www.youtube.com/watch?v=K43blwpLw1U&t=1001s): a survey talk on Youtube. I had seen some of the mathematical topics of graph theory and rigid structures, but hadn't realized they could be used for multi-lateration before; that's pretty neat! 
- Various localization papers: I will of course have to read more about localization algorithms, such as [least squares](https://link.springer.com/article/10.1186/s13638-015-0298-1), Kalman filters, and [robust estimation](https://www.ri.cmu.edu/app/uploads/2017/04/eskf.pdf). While least squares is just basic linear algebra, and I've covered Kalman filters in a previous post, I should make sure I understand details about how to apply them to wireless networks. And what the heck is "robust estimation"? That's so vague...

In the future, time permitting, I'd love to learn more about the [scattering theory](https://en.wikipedia.org/wiki/Scattering) of physical waves; this seems highly interesting. I would also be excited to read more about Poisson processes, as I hear that they are useful for modeling how wireless networks change as users move about in a room. But for now, let's scramble to meet deadlines...

