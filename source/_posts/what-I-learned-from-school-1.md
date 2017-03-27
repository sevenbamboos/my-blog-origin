---
title: What I Learned From School Part 1
date: 2015-10-23 20:43:30
categories: Lifestyle 
tags:
---

## Mathematics, statistics and numerical methods

> The course of “Fourier analysis” mainly focuses on the idea of mapping a function in the time domain to the one in the frequency domain. 

I did not realize the usefulness in signal processing until I did a research on the compress algorithm of MP3, which is a file format to store sound wave in file system. When computer records a sound, first it receives the sound wave via a microphone. The sound becomes electronic signals and after processed by sound card, it is saved into file system with the original format WAV. Fourier transformation can convert the original signals (in the time domain) into a series of numbers which represent offsets on different frequencies. In this way, MP3 has enormous compression rate compared to the WAV format and therefore nowadays a portable music player is able to keep thousands of songs.

<!-- more -->

> In addition, with the help of Fourier transformation, we can analyze tones of music. 

For example, there is a popular application on mobile phone, which is able to match a pop song from its huge music library after analyzing the sound wave from microphone.

Nevertheless, to achieve a higher compression rate, MP3 conversion will discard parts of tones with high frequency that is hardly audible for human beings. So it is not a lossless format and is not suitable for hi-fi equipment.

> Another example is from the course of “linear algebra”, which focuses on the calculation of matrices and vectors. 

In my current work, we maintain an image application for medical study, which can help radiologists diagnose diseases. If the image is 3D, there will be a picture-in-picture alongside, which is a 2D projection by the following transformation: 
```
[ x ]   [ 1, 0, 0 ] 
[ y ] x [ 0, 1, 0 ] 
[ z ]
```

There are other transformations such as reversing, rotating and enlargement. In theory, an image can be enlarged unlimitedly. But in practice, the limit is the pixel dense of the original image file. If the image has to be enlarged over that limit, the transformation has to fill in extra pixels depending on detailed algorithms, which can cause a fuzzy outcome.

## Statistical variability

As a software developer, one of my responsibilities is to analyze performance data and fine-tune source accordingly. In my current job, the connectivity module that I maintain has two separate processors: one for Admit Discharge Transfer (ADT) message, the other for Order Message (ORM). They are different processors and should have no dependency. 

From the result of performance data, the processing time of them has a rather high correlation: 0.5. So I investigated in this direction and found that near the end of processing, both processors will trigger an asynchrony downstream workflow, which shares a common code base and had a performance issue. 

> So it explains the unexpected high correlation and the finding is based on the knowledge of “probability and statistics” course.

|ADT(a)ms	|ORM(b)ms	|a	|b	|a*b	|a*a	|b*b|
|-:|:-:|:-:|:-:|:-:|:-:|:-:|
|747	|1172	|-271.4	|-1029.4	|279379.16	|73657.96	|1059664.36
|412	|2800	|-606.4	|598.6	|-362991.04	|367720.96	|358321.96
|2807	|4594	|1788.6	|2392.6	|4279404.36	|3199089.96	|5724534.76
|832	|1926	|-186.4	|-275.4	|51334.56	|34744.96	|75845.16
|294	|515	|-724.4	|-1686.4	|1221628.16	|524755.36	|2843944.96

|Mean(x)	|Mean(x)			|Sum(a*b)	|Sum(a*a)	|Sum(b*b)
|:-:|:-:|:-:|:-:|:-:|
|1018.4	|2201.4			|5468755.2	|4199969.2	|10062311.2

## Knowledge of probability, statistics and calculus
E2.4 Knowledge of trigonometry, probability and statistics, differential and integral calculus, and multivariate calculus that supports the solving of complex engineering problems 

> I use the knowledge of “probability and statistics” in another fine-tuning of software performance. 

I designed an in-memory component which in rare cases could fail to notify other nodes in a cluster environment of the latest change in database, and which in turn can cause unsynchronized error. Now a workaround is that we add a check to the nodes to synchronize with database at regular intervals. I need to give an analysis on how long we need to set the interval to reduce the probability of error to a certain level.

Suppose we know the probability of the notification failure P(N) and the notification success P(N’), then the probability of the error has the following expression: 
```
P(E) = P(E|N)P(N) + P(E|N’)P(N’) (E2.4.1) 
```
When the notification works, it is impossible to have this error. So the expression E2.4.1 can be simplified to: 
```
P(E|N) = P(E) / P(N) (E2.4.2) 
```
Before we add the check, P(E|N) = 1. With the timely check, there is still a probability of getting error because: 
`P(E|N) = (T – 2a * T / t) / T` (a is a short interval that is regarded as an acceptable period to wait for data synchronization; t stands for the target interval; T stands for a given period of time unit, e.g., a minute or an hour). The expression E2.4.2 can be further simplified to:
``` 
P(E|N) = (t – 2a) / t. (E2.4.3) 
```
Suppose we want to reduce the error level to 1%. And suppose 2 seconds is the acceptable waiting time, then:
``` 
t <= 2a / (1 – 1%) = 4 / 0.99 (s) (E2.4.4) 
```
So the solution is the node needs to perform the check every 4 seconds.

I learned multivariate calculus in mathematics course, which has the following use in the management of software projects. I am the team leader of a group of software developers. I notice that the overall story points (an indicator used in Scrum methodology to measure performance) of the team in a certain period of time is a function of working hours H and expenditure E (including labour costs, entertainment fee, and so on): P(H, E).

Now suppose the marginal point of working hours is proportional to the overall performance per working time: 
```
∂ P / ∂ H = a * (P / H) (a is a constant) (E2.4.5) 
```
When E remains the same (E₀), it becomes a differential equation: 
```
P(H, E₀) = k * Hª (E2.4.6) 
```
Similarly, from the partial derivative for expenditure E, we get: 
```
P(H₀, E) = m * Eᵇ (E2.4.7) 
```
Combine E2.4.6 and E2.4.7, we get: 
```
P(H, E) = k * m * Hª * Eᵇ (E2.4.8) 
```
Suppose it conforms to the Cobb-Douglas Production Function (suppose a + b = 1), the final equation is: 
```
P(H, E) = c * Hª * E¹⁻ª (c is a constant) (E2.4.9) 
```
With the help of E2.4.9, I have quantitative knowledge of team’s performance and understand how to arrange resource to achieve team’s potential.

## Differential equations in time-dependent processes

> Differential equation is one of the most important applications of calculus (“advanced mathematics”). 

We can describe a phenomenon with a differential equation. With the analysis to sample data, we can find a formula for the equation and display it with graphics. It is very useful for software development.

For example, modern CPU has multi-cores, which enables Operating System (OS) to create hundreds of threads to process data simultaneously. But each thread needs extra resource and switching among them too frequently can cause performance penalty. Therefore for a fully loaded web server, a technical question is how many threads we should configure in thread pool to make maximum use of the capability of hardware. 
Supposing that very few threads exist when sever starts up and we set this moment as the start time. Gradually, external requests start coming and the server starts creating new threads to serve them. Depending on different OS, some have specific algorithms to create a number of new threads to serve concurrent requests. Supposing that the change rate of threads is proportional to the thread number, we have:
``` 
d(C) / d(t) = a * C (C stands for thread number, a is a constant) (E2.5.1) 
```
After a certain period of time when the thread number reaches a threshold, the thread number increases slowly and finally stops growing. The underlying reason is that all threads are busing with data processing and the average processing time becomes increasing so that it seems that CPU has not enough resource to create new thread. So in this case, the growth rate of threads will drop and the expression of the change rate of the thread number becomes:
``` 
d (C) / d(t) = a * C ( 1 - C/H) (H stands for the threshold) (E2.5.2) 
```
The meaning of this equation is when C is small enough, the growth rate is close to a * C. As C grows, the rate becomes slower and finally stops growing. After solving this equation, we get a function in time domain of the thread number:
``` 
C (t) = H (1 + b * e ⁻ªᵗ)⁻¹ (b = (H - C₀) / C₀ (C₀ is the figure of thread number at the start time) (E2.5.3) 
```
We also know that `lim C(t) = H (when t -> ∞)`. With the help of this equation, we know how many threads should be initialized in thread pool to have the best performance. We also know how many servers we need to set up as a cluster to handle large number of requests.