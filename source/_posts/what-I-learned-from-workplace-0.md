---
title: What I Learned From Workspace Part 0
date: 2016-01-14 20:59:00
categories: Lifestyle 
tags:
---

## Research and analyse in design

Before designing a software system, I will do use case analysis first, which helps me understand how the system interacts with external resource. Sometimes, a clear user case diagram also serves as a good communication with non-technical participants. Besides, performance specification also needs to be taken into consideration either with pseudo-code or prototype. In the passed 10 years as a senior software developer, I participated in the requirement analysis of enterprise applications in multiple domains ranging from manufacturing (2001), auction (2002-2006), entertainment (2006-2009) to healthcare.

<!-- more -->

## Investigation and identification of system

Since 2007, as a software developer (in Kodak product development centre and Agfa healthcare), one of my responsibilities is to analyse abnormal behaviour of a software program and provide solutions. Based on log files (stack trace), screen shots or the steps to reproduce , I am required to investigate the root cause, provide a fix or a temporary workaround if acceptable. To do this, I start from building a test environment to reproduce the issue. If not possible, I will read source code, try to reveal the cause from the result (it can be challenging for concurrency issue like race condition). I will do experiment in local environment to prove my hypothesis (Please refer to Element One for the discussion about scientific method). After that I will give my solution as well as an analysis of the impact of my change so that testing team can double-check the issue in regression test.

## Develop from principles

I use UML as a language of design. The nouns appearing in use case are candidates of domain models and their behaviour can be methods of these models. I will give a draft design with a high-level class diagram and deployment diagram. At the beginning of a project, I prefer not to put many details in the design diagrams as I believe that great software is not likely to create by design but by code refactoring. Design languages such as UML provide a way to communicate idea. But it can still include errors that are hard to detect (e.g., incorrect dependency between modules, unnecessary class hierarchy. Please refer to the project of connectivity module for details). What is more, an over-designed class diagram could bring more trouble than no design. In my current job, I do design cycle by cycle with an incremental approach, which is suitable for Scrum methodology.

## Design methods and tools

I often use design pattern to explain the structure of my design. Classical patterns (Group of Four) appear many times in my design document. More importantly, I use Single-Responsibility Principle (SRP) and Open-Closed Principle (OCP) to explain the purpose of the design.

SRP emphasises on the idea that a model should include all relevant details and expose as little as possible to the outside world. It encourages to design self-contained components.

OCR is mainly about how to build loose coupling components. A component should be open for new functionality and close for irrelevant change. It has a well-known extension: Inversion of Control (IoC). Nowadays, many application frameworks provide IoC service to avoid strong dependencies between components.

With the evolution of technology, some design patterns become less important. For example, with the support of lambda calculus in Java and C#, command pattern and strategy pattern are not necessary. But the principles remain the same.

## Decision making and problem solving of engineering problems

My model-based framework (2006) was under great influence of these two principles. An example is Mapper tool (2013), a middle ware on server side. In the first iteration, following OCR, I designed each sub-module (expression parser, executor, language package and runtime) in a loose coupling relationship. But the disadvantage is that the runtime had to interact with language package indirectly, which introduced much complicated source code and made the debugging very hard. As a result, I made a tradeoff between OCR and SRP and solve the problem by combining language package and runtime. It took two iterations for me to realise the optimum design and another two iterations to finish the code refactoring.

## Analyse feasibility of projects

There are several factors which can affect feasibility of a project. The first one is how to define the scope. I once joined a project (2001), in which the customer hoped to improve manufacturing workflow with the new Enterprise Resource Plan (ERP) system. But the requirements of the new system were based on the current workflow. So even the system can help increase the productivity to some extent, it is impossible to meet the original needs and therefore failed at last. This factor requires customers and project management to align their expectation for the outcome at the beginning of a project.

The second factor is the performance specification. For example, the architecture of an online auction for 100 people is totally different from that for 100000 people. In 2005, I joined in a project to develop a program for online auction. I quickly built a web-based prototype ("Rapid prototyping" is a useful course, that gave the idea how to use prototype to analyse the feasibility of a project). After the analysis to the prototype, we found there was a big gap in performance and therefore we reached an agreement that the auction rule needs to be changed to avoid too many concurrent requests. So it is important to foresee this kind of difficulty in software design.

The last factor is cooperation with team members (including members from external team). A clear job-sharing and good communication plays an important role in (big) software projects. In a big project of a radiology information system, new release comes out every cycle (3 months) without much delay, I ascribe the success to the close communication between development teams.

## Design experience that includes various constraints in the real world

I will use my experience in a project of radiology information system to illustrate my abilities in software design. I designed a component to process medical data every day and I have taken into consideration of a number of aspects.

First of all, reliability is the most important in this design. The component is supposed to provide 24x7 hour service to process data between multiple systems. Additionally, any mistake inside the component could result in the data loss of a medical report or a medical study, which would be a severe issue of patient safety. As a result, Application Public Interface (API) is required to 100% test coverage and user scenarios need to be covered by automation test as much as possible. To meet this requirement, I’ve changed my design twice to make sure every sub-module is self-contained and testable before being deployed in a real production environment. Therefore, over 100 test cases have been designed and run every day on the continuous integration (CI) server.

To improve user experience, a tool is provided with fancy features like drag&drop, double click and inline validation message to help user configure the component. More practically, a syntax-check (real time) is shipped with an expression editor which can detect configuration errors. Apart from a GUI tool, I built another tool on server side to enable administrators do urgent change and can be used to monitor runtime status.
