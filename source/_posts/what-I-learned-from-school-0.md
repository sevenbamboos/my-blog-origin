---
title: What I Learned From School Part 0
date: 2015-09-06 20:43:30
categories: Lifestyle 
tags:
- physics
- scientific method
- Theory of Relativity 
---

## Fundamental quantitative knowledge of physical world

> In "physics" course, I learned the following fundamental concepts.

Mass is an important property for a physical body. For example, to build a software model for the movement of a racing car, we need to calculate its mass by 
```
∫density * d(volume)
```
In practice, a car consists of many parts and each of them has different densities. So an approximation is:
``` 
∑ density-of-part * volume-of-part = density-of-engine * volume-of-engine 
+ density-of-tyre * volume-of-tyre + density-of-chassis * volume-of-chassis
+ …
```

<!-- more -->

The second concept is Newton’s Second Law of Motion, which is the fundamentals of Kinetics. In a 3D world, the motion in each component can be described with the equation: 
```
∑ Force = mass * acceleration
``` 
Supposing that we are developing an angry-bird like game and need to analyze the path of a flying bird. If we can ignore the wind or the drag caused by air, then the speed on the x-component remains the same, while on the y-component, a constant acceleration (gravity) is taking effect. This information is sufficient to build a physical model for a video game. If it is a 3D game, we also need to add the force analysis on the z-component.

The next and maybe the most significant concept is the atomic hypothesis. Atom’s motion can explain heat and mechanics phenomena. Atom’s electrical force can explain phenomena of electricity and magnetism. More importantly, it leads to quantum mechanics, which describes why at high frequencies, waves in electromagnetic field behave like particles. It means wave and particle might be two aspects of the same thing. For computer science, it is of importance because computers have difficulty in calculating continuous data like integration. Instead, computers use discrete sample data to provide approximate results. If progress is made in quantum computer, it can help build a better model for the computation of the physical world.

> In addition, "physics experiment" course tells me that no matter how many times we repeat an experiment, the result is unlikely to be exact the same as the figure calculated by laws. 

The same thing happens in computer science. For example, we cannot generate a real random number by a software program or get the accurate computation time (system clock is a timely signal coming from crystal oscillator). But in practice, we can prove that the overall result conforms to mathematical expectation. As a result, for software engineers, it does not make sense to try in vain to look for a perfect solution. Instead, an appropriate solution which can meet the needs is sufficient before a better solution is found.

## Scientific method and problem solving

> In the course of “Dialectic of Nature”, I learned the fact that humans are in pursuit of truth, but have not revealed universal truths. 

To put it in another way, the laws in course books are not truths but approximations. On the other hand, these approximations are helpful in understanding thing in small scales or in the scope that we know for sure and then we can extend them to larger scales to find out better answers. In the pursuit of truth, the only method that we can rely on is to experiment. This illustrates what is the scientific method and I carry out the spirit in software design as following:

+ The first step is to observe a requirement. In software development, I start from known conditions, carry out analysis in the domain of the problem and have discussions with domain experts to clarify the input / output of the system and how the system is supposed to be used (what is use scenarios).

+ The second step is to make judgements. I give my proposal or a draft design based on the result of the previous step, together with the domain knowledge and experience in similar problems. Sometimes, I also use my imagination or intuition as it is an important way from the known to the unknown.

+ The last and also the most important step is to experiment. I do experiment with source code to test my design and then do experiment with a running system to test the source code, which in turn is verified by software testing and customers' feedback.

I also use the scientific method in problem solving. My approach is to start from a small scope until I reach a specific rule and then broad the scope to build a generic rule. Basically, in computer science, the divide-and-conquer approach is a typical use of this idea and I will explain it later.

## Application of knowledge to solve engineering problems

I have used physics knowledge to design a model for an online auction, which has the following rule:

> Each participant bid for an item on sale with a bargain price that has to be higher than the current price. If at the end of an auction, the highest price comes from multiple participants, then the one who submits the price first will win. 

At first, the design was using the machine time of the application server as a global time. But the auction server became too complicated to process requests from clients and update its status with all other clients. The server could also become the bottleneck with the increasing number of clients. On the other hand, the application on client side relies on the notification from the auction server to update UI or to do validation on the input. The high network latency could affect its responsiveness.

Inspired by Einstein’s Special Theory of Relativity, especially the concepts of affective past and affective future of space-time interval, I gave a new design, which does not need a global time. Instead, each client becomes an element of a vector, representing events from clients. At the beginning, each element has been reset and the auction server will broadcast the vector to each client. Client will update itself according to other client's events and send the vector back to the server after updating its event in the vector. During an auction, whenever a client gives a bid, the event will be sent to the server and then broadcast to other clients to update everyone's status. On one hand, it saves the computation time of the server. On the other hand, the distributed computation performed on client side provides better user experience since it is performed locally without network delay.

The design has not been put into use just because the auction server could have performance issues to broadcast events to all clients. In spite of this, the new design brings about a clearer model which reduces the complexity of source code.