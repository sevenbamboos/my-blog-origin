---
title: Mind Map for Java
date: 2017-11-10 15:38:14
categories: Mind Map
---

# Category

Section | Contents
--- | ---
Basic | [Basic](#Basic)

<!-- more -->

# Basic
Question | Answer
--- | ---
01.Classloader | Three classloaders: Bootstrap(rt.jar), extension and application(classpath).
02.OOPS principles | Abstraction with encapsulation, (type system supports) inheritance and thus polymorphism.
03.Object oriented VS object based | ES5 as object based language, provides abstraction and encapsulation, but no native support of inheritance and polymorphism
04.What happen if multiple interfaces have the same default method and a class implements these interfaces | Compile error.
05.Aggregation VS composition | Objects get destroyed along with the composite object, while aggregated objects exist.
06.super() and this() | They (not both of them) must be the first statement for a constructor.
07.Execute a program with main method | No way to do it since Java 7.
08.Override for static(class) method | No way as inheritance doesn't apply to class.
09.Overloading VS overriding | Overloading happens at compile time, while overriding at runtime.
10.Covariant return type | It's allowed to return a sub-type in method of sub-class. (see code)
11.Marker interface VS annotation | Interface provides static check during compiling, while annotation has to be checked at runtime. (see code)

Continue at P163

```
//10
class Animal {
    public Animal giveBirth() { return this; }
}
class Cat extends Animal {
    @Override public Cat giveBirth() { return this; }
}

//11
@SomeAnnotation
class Foo { ... }

  if (!obj.getClass().isAnnotationPresent(SomeAnnotation.class)) {        
    // do something       
  }   
  
```