---
title: What I Learned From School Part 3
date: 2015-12-19 21:59:00
categories: Lifestyle 
tags:
---

## Knowledge to support practice

> I chose software as my research direction from the third year of university under the influence of “data structure” course. 

As what the name suggests, the course helped me understand two basic elements of a program: data structure and algorithms, and their relationship.

The order of growth (𝛩-notation) is the most fundamental concept to help understand why various data structures and algorithms are designed. For instance, most programming languages provide support for array or list, which stores data in a sequence of blocks of memory. The expectation of the time to look for an item in an array is 𝛩(n). It is an efficient data structure for cases that the number of the items are small. But there are exceptions.

<!-- more -->

> When debugging a radiology information system (2015), I found a search function of an array is extremely slow. 

The function looks for an external system in an array of dozens of items. The root cause is that each item is persistent as a JSON object. During the search, each item needs to be parsed and converted into an object tree. So even with only dozens of items, the array is still not a good choice. My approach is to use a hash table as a memory cache. Hash table is a table with (key, value) pair and has 𝛩(1) for search operation. I put the identity of an external system into the hash table as key, the parsed object as value (so each item will be parsed once during the lifetime of the application). As a result, the change improves the performance more than 100 times.

But hash table is not the answer to all criteria. In a task (2014), I was asked to build an index for billing codes (the total number can be over 100K in some countries). Supposing the billing number is chose as the index property, then one billing code needs 4 bytes (32 bits). The total of billing codes will result in 400K of memory usage. What is more, hash table needs at least twice of the capacity to put hash key, which means the whole index needs to consume 1M of memory. So even with the expectation time of 𝛩(1) for search operation, hash table is not suitable in this case (bitmap is used instead).

I am also familiar with other data structures such as trees and graphs. I spent much time building parser and processor for HL7 / DICOM messages, which are certain kinds of tree. The common strategies to visit a tree are deep first search (DFS), breadth first search (BFS) and hill climbing search. The basic approach to solve tree-related problems is divide-and-conquer and recursion. That is to divide the question domain into multiple sub-domains, repeat the step recursively until sub-domain is small enough and a straightforward solution appears. The final step is to combine the solutions together. The approach can prove by Mathematical Induction that the overall cost can be much better than the solution of brute force. For example, supposing we divide the domain into two parts and need 𝛩(n) to combine the results, then the overall cost is 𝛩(n lgn).

Regarding graph, it shares some common algorithms with tree but has its own specific use. I was once asked to calculate a path on an image of Digital Radiography (DR) study. The path needs to go through a group of points on the image. This is a shortest-path problem in a graph, just as how a navigating system finds a route on a map. The approach is to take the points as a network and divide them into multiple layers so that in each layer there are very few points and I can use brute-force to loop through all of them to get a local solution. I use the greedy algorithm to decide how many layers I need. And the final step is to combine local solutions together.

Generally speaking, the knowledge of data structure and algorithm gives me the ability to design complex computation units. And I also gained knowledge about software engineering theory and object-oriented design principles, which gives me the ability to organise these computation units.

## Systematic understanding

> I took an elementary course of “software engineering” (2000) and a programming course (2001) based on Java technology for my bachelor degree. 

Waterfall model was used in the course at that time, which borrows idea from manufacturing workflows. It treats software development as sequential processes (analysis, design, implementation, testing and maintenance). In practice, software projects following this method have a high rate of failure due to the frequently changed requirements. For example, the problem found during the test phase can be a disaster for development team because it can cause a large scale of code refactoring or even the change to the design model. As a result, after leaving university two years, I took software engineering course (2003-2006) for a master’s degree. The courses include Introduction to Software Engineering, Software Project Management, Software and System Rapid Prototyping, Software Quality Assurance, Software Implementation and Testing and Software Maintenance and Re-engineering Evolution.

In this period, Agile methodology emerged as an answer to the limitation of Waterfall model. I involved in many discussions of this topic. Generally, I believe Agile method is a repetitive waterfall model but with two good practices: Test-Driven development and refactoring. In an Agile method, we still follow sequential processes but repeat them every a certain period of time. On one hand, big features can be split into small tasks which can be scheduled in multiple cycles. And tasks with smaller scope tend to be easier to tackle. That is the idea of divide-and-conquer algorithm. On the other hand, product owner can give feedback to the product at the end of each cycle so that developer can improve it in next cycle. It acts as a feedback system for software projects just as the Nyquist stability criterion in control theory and stability theory, which reduces the cost of doing change compared to that in waterfall model. I have more than 6 years of experience in a Scrum team, working on a big project with cross-site cooperation and have the chance to practice what I learned in the above software engineering courses.

## Application of complex engineering problems

> Regarding programming skills, I got Java certificated programmer in 2001. 

Java was a young programming language at that time but I believe object-oriented design would be a trend. As a high-level programming language, Java language has a clear syntax and good support in open-source community. That is the reason why I invested a large amount of time in this technology. Most projects that I took part in were based on Java platform (2003-2006). Meanwhile, I also took a series of courses in object-oriented development: Object-oriented Method, Software Modelling and Design, Object-oriented Formal Methods, which improve my ability to design flexible software systems.
