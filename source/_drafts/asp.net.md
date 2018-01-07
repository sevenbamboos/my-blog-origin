---
title: ASP.NET For JavaEE Programmer
date: 2017-12-30 10:33:00
categories: Programming
---

It's based on ASP.NET Core, which is the latest version with VS 2017. I list the feature unfamiliar to JavaEE programmer.

# Sections

Title | Link
--- | ---
ASP.NET Core MVC 2 | [Goto](#ASP-NET-Core-MVC-2)

<!-- more -->

# ASP NET Core MVC 2

Question | Answer
--- | ---
01.For validation failure of a post form, `return View()` returns to the form with the previous inputs. Why doesn't it need to pass view the form model? Why does the view still keep the model? | TODO 
02.When to useViewBag | A dynamic object without IDE's auto-complete suggestion. One way is to use it to control how to render model, which means it includes hints about how to display model but not part of model.