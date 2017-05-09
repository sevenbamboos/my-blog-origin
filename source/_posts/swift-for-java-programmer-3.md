---
title: Swift For Java Programmer 3
date: 2017-05-09 22:47:07
categories: Programming
tags: 
- Swift:
---

In the third series, let's start the part of class. In Swift, class is reference type, which has a lot in common with this concept in other programming language like Java, c# and Javascript.

# super
For a normal override method in sub-class, it's always better to call its super method at the very beginning of the method. Otherwise, the logic in sub-class could get overwritten by the base class.

For the same reason, inside init, sub-class MUST call `super.init` before returning. It's called two-phase initialization.

    class SubClass: BaseClass {
      var sth: String
      init(...) {
        // phase 1 start
        self.sth = ...
        super.init(...)
        // phase 2 start
        self.foo()
      }
      foo() {...}
    }
We can't call any instance method until phase 2 because at that time the initialization has been completed.

<!--more-->

# init
TBD
