---
title: What I Learned From Workplace Part 2
date: 2016-03-13 22:24:00
categories: Lifestyle 
tags:
---

## Catch up with the trend

I have advanced knowledge in both connectivity of healthcare systems and web-based technology.

In 2014, I designed a processor for HL7 messages (in Java). The next generation of HL7 standard (FHIR), is a RESTful protocol using JSON format to transfer messages. To make better use of the new protocol, it is better to use the architecture of full-stack Javascript. That is to use a NodeJS sever as the connectivity module to handle FHIR requests and to use NodeJS client to do integration test. In this way, we can unify the data format as JSON on both server side and client side and save the time of converting data back and forth between two sides. I work with my colleagues on this new architecture and I contribute part of my work, a HL7 message parser written in pure Javascript, to Github and publish it in Node Package Manager (NPM) under the name “sam-utility”. The new architecture can simplify the structure of connectivity module and most providers of radiology information system can benefit from it.

<!-- more -->

## Theory and practice and their extension

New technology in software engineering is unlikely to be based on isolated facts alone but on laws which connect existing knowledge and practice and the development is a cycling process marked by its continuity and coherence.

For example, many companies (including my current company) are upgrading architecture of software products with web-based technologies such as HTML, CSS and Javascript. Basically, the major upgrading involves moving computation from server side to browsers (or mobile devices). As a result, new frameworks appear to help developers to build sophisticated applications on client side. When I compare these frameworks with those server-side frameworks which were created in the passed decade, I find that they are derived from the same family of design pattern: MVC (or MVP, MVVM or other MV* variations), which means I can reuse the familiar concepts I learned in the course of design pattern to understand the new technologies.

## Progress in cutting-edge technology and science

I will use AlphaGo, Google’s program for the chess of Go, to illustrate the achievement in two areas: big data and machine learning.

Generally speaking, two artificial intelligence algorithms are required for a chess program: one is how to evaluate the value of the board; the other is how to choose the position for the next step. Last century, deep blue program (IBM) beat the best chess player with a hard-coded function written by human. But Go has a much bigger board and therefore it is not possible to evaluate a full branching tree to cover all possible ends of a match. Instead, AlphaGo uses Monte Carlo Tree Search (MTCS) to help evaluate the board by reducing the branching tree. More importantly, AlphaGo’s evaluation and decision making algorithms can be improved by self-learning. In fact, during development, it improves its own’s algorithms by analysing thousands of matches. As a result, AlphaGo’s capability improved from an amateur to a world-class player in a few months.

In practice, AlphaGo provides an impressive example of what the next generation of computer program looks like: collect analysable data and provide intelligent strategies. I am doing research on this direction for healthcare systems.

## Research in specific engineering areas

Instead of published books, software technology is more likely to first appear on the internet. Therefore I registered in technical sites such as JavaWorld, TheServerSide, IBM developerWorks, O’REILLY OnJava, Microsoft Software Development Network (MSDN) to receive the latest progress. After comparison and synthesis of technologies on JavaEE platform, I came up with my own application framework in 2006. Please refer to my dissertation on this topic (a bibliography in included near the end of the dissertation).

Since 2010, I take part in the procedure of reviewing design documentation in the current company. My review scope includes component design in connectivity modules and a desktop program for administrator. Meanwhile, I gain cutting-eager knowledge by subscribing weekly newsletters (Javascript, HTML and Swift). In return, I contribute part of my work as open source on Github under the name “sevenbamboos”.

## In-depth learning and education

In my career, I took part in many projects based on Java technology and am interested in this topic. I took Java programming course in university, continued the self-learning and got Sun Certificated Java Programmer (SCJP) in 2001, which helps me to use the technology better in my work.

In 2003, I returned back to university and took courses in development methodology of software and got master degree of software engineering.

After I joined Agfa healthcare, I take part in the development work in medical systems and participated workshops in Gent Belgium in 2014 and 2016, discussing the latest progress in Health Level Seven International (HL7) and Digital Imaging and Communications in Medicine (DICOM) standard.

## Stay hungry, stay foolish
I have work experience in a wide range of programming technologies (C, Java, Python, Ruby, Lisp, C#, Object-C, Swift and Javascript) and have the knowledge to build software on various platforms (JavaEE, .NET, IOS, NodeJS). Most of them were learned via on-line course, articles, mail groups, books and practice. These knowledge makes me a competent software developer and a component architecture in various domain.

Since 2010, I participate in Academy Learning Platform (Agfa) and have taken over 70 courses, ranging from software engineering to domain knowledge of healthcare. These knowledge makes me a competent software engineer in healthcare domain.

In 2016, I self-learned Web development and get the front-end certificated programmer of freecodecamp.
