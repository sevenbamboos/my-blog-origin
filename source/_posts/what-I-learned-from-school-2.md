---
title: What I Learned From School Part 2
date: 2015-11-16 22:35:00
categories: Lifestyle 
tags:
- computer composition
- Turning machine
- digital logic&logic design
- assembly language
- compiling principles
- Operating System
---

## Define key information in fundamental engineering

My specialization is computer science and software engineering, and my knowledge of this part starts from Turning machine, a mathematical model of computation. 

> From the “computer composition” course, I learned that a Turning machine is a Finite State Machine (FSM) and consists of a number of functions, which is able to change FSM from one state to another based on the input. 

A more general concept, Universal Turning Machine (UTM), is a Turning machine that can turn into any Turning machine with the description of the target machine as input. It means we don’t need to build specific machines for specific tasks.

For me, the theory of Turning machine has influence in two areas. One is that it helps to understand the modern computer systems (which I will describe later). The other is “Turning complete”, a way to measure the capability of a computer. There are four elements in “Turning complete”: unlimited storage, ability to do arithmetic, evaluation with condition and the support of repetition of execution. They are mandatory for a model with maximum capability of computation. Once we find them in a model, we can prove it has reached the limit of computation and therefore is “Turning complete”. That is the reason why I spent a large amount of time in my dissertation of a model-based framework (2005) to implement functional programming (or formally Lambda calculus) in an imperative programming language. Recently (2014), I implemented a Domain Specific Language (DSL) for healthcare business and filled a gap using this thesis.

<!-- more -->

Modern computer systems consist of a number of modules and sub modules, including Central Processing Unit (CPU), storage, the controllers to manage input / output devices and other external devices such as monitor, printer, massive storage (hard disk), network and so on. These modules talk to each other by an electronic pipeline called “bus”. In fact, the concept of bus is so widely used that it affects the design of software. For example, I prefer using event bus to connect multiple components to achieve the result of loose coupling. It builds communication between components without introducing strong dependency. It can also be used in the mode of “subscribe and publish”, which can further improve the flexibility of a system. For example, I worked in a Model-View-Controller (MVC) application. Controller receives data from model and updates view. Meanwhile, it talks to other controllers via an event bus so that all parts of User Interface (UI) get updated.

Another concept that gives me inspiration is cache. Compared to high-speed of CPU, storage works much slowly. In normal usage, computer system sends the large amount of data between modules and it is very likely to be the bottleneck of the system. The solution is to provide a high-speed cache to keep temporary data. Bear the concept of cache in mind, I designed a connectivity component with compiled expression tree in memory (instead of in database) to improve the network throughput. (2013)

Computation happens inside a CPU, so the understanding of CPU plays an important role. 

> In “digital logic&logic design” course, I learned how to design a CPU with Hardware Description Language (HDL). 

It uses logic gates as an elementary unit and based on the theory of Boolean algebra, logic gates can form into large computational block called combinational circuit to implement complex logic. Emulators are used to run the compiled HDL and the result can be verified without creating a real Integrated Circuit.

Once CPU is ready, we can give it instructions to do computation. For this purpose, every CPU has been designed to understand a language called machine language. 

## Concepts and principles to across a broad spectrum of engineering 

> “Assembly language” course gives me knowledge about how to do programming with this low-level language. 

For most software engineers, it is not the most commonly used language but it can be used by a middle-level language (e.g., C) to provide a hardware specific (or performance critical) solution. It also helps when fine-tuning a program written in higher level languages. Through the analysis to the generated assembly code, I can optimize the source code of higher-level language by changing the configuration of the compiler. 

> It also involves the knowledge of “compiling principles”. I took my first programming course, “fundamentals of programming”, in the second term and C programming language was used as teaching material, which is a middle-level language. 

It has a clear syntax and provides an abstract layer for hardware, which enables me to focus on business logic (data structures and algorithms) instead of the details of how to manipulate hardware. It also helps to improve the program’s portability with less chance of incompatible issue. C programming language can be used to build drivers and provide Application Program Interface (API) for high-level languages to control hardware. It still has a large population of developers worldwide and is heavily used in the development of embedded systems and home automation.

> I took “Operating System” (OS) course in the third year and an extension course (advanced UNIX programming) during my master's degree. 

Basically, OS stays on top of hardware and provides service to access CPU, storage and I/O devices. OS also has a scheduling system to run multiple programs simultaneously. Process is a running program with the exclusive use of a virtual memory space. OS cuts clock time into small slices and chooses a waiting process to run in each slice. Inside one process, thread is a lightweight solution for concurrent computation. I have some experience in performance fine-tuning for multi-thread program during the project of online auction.

The concepts of process and virtual memory space play an important role in understanding programming languages. In fact, it involves a number of pitfalls that I was not aware of them until I worked for Kodak digital cinema division, where I spent much time debugging memory use of a video player.

The top part of the virtual memory is for stacks, which is used when a program invokes a function. Each stack will be allocated with local variables and a return address. When the program returns from the function call, the stack will be deallocated. For local variables of value-type, they will be cleared along with the stack. For those of reference-type, their reference pointers in the stack will be cleared, but not the data in the heap (which is allocated via system function malloc). This is one of root causes of memory leak. 

## Fundamentals to solve complex engineering problems 

Different technologies have different solutions. Either programmer need to free the data in the heap manually (e.g., C and C++), or rely on reference counter implemented inside the runtime of the language (e.g., Object C). A more advanced (but may slower) solution is that the runtime performs a scan on the heap at regular intervals, looking for all allocated memory that have no external reference and mark them as candidates for garbage collection (e.g., Java, Javascript, C#).

Another pitfall comes from the misunderstanding of how parameters are passed during function call. If they are passed as values, then the program has to do copy for these parameters before doing the function call. If they are passed as references, only the reference pointers have to be copied, not including the data in the heap.

Without the understanding of stack and heap, it is unlikely to be successful in software development. That is the reason why I always pay attention to how programming languages do memory management.

> I learned from the course of “Network” about the TCP/IP protocol suite. 

The model of abstraction layers (link, internet, transport and application) has wide influence on the multi-layer architecture of software such as JavaEE core pattern, which in turn is a reference of my model-based framework “SGMF”. I also acquired knowledge about cryptography, which was used in a project of online auction (X.509 certification authority and Denial of Service), in a project of video player with embedded chip (RSA).

> I learned from the course of “Database” about Entity Relationship (ER) model and database normalisation, which give me the ability to design database in online auction project, connectivity module of healthcare system and other projects. 

The knowledge of structured query language (SQL) gives me the ability to fine-tune programs with object relationship mapping (ORM) frameworks such as "Hibernate", JPA and ADO.net Entity (I wrote an article about how to do ORM in Entity framework with samples from real projects in my Github blog). A recent case was that on an Oracle database, I replaced “sysdate” with “currentTimestamp” in a database query to fix a subtle issue happened only when database and application are installed with different time zones.

To sum up, these courses give me the fundamental knowledge to continue the study of specialist courses and work as a software engineer.
