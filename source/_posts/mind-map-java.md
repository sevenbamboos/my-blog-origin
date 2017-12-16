---
title: Java Basic
date: 2017-11-10 15:38:14
categories: Programming
---

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
12.Pass-by-value or pass-by-reference | Java is always pass-by-value. (see code)
13."foo" VS new String("foo") | The former is in string constant pool, while the latter is a normal string object. (see [this](http://www.thejavageek.com/2013/06/19/the-string-constant-pool/)) 
14.Comparator VS comparable | The former is a util interface comparing two objects, while the latter compares another object with itself, which means it is implemented by compared types while comparator is used by sorting functions.
15.Why Object::hashCode() is native | Performance concern because it looks for an integer representation of an object reference on the heap. 
16.What's the benefit of Collections::copy() over collection constructor or addAll | It doesn't involve reallocation but instead trigger out-of-index error if happened.
17.Return value for high-order function | For functional interface, return a lambda (instead of an anonymous class).
18.java.time package since Java 8 | LocalDate, Year, MonthDay, DayOfWeek. Also refer to TemporalAccessor for use suggestion (see code)
19.@SafeVarargs | For methods with variable number of arguments, JVM uses array under the hood. When the type of arguments is type parameter, JVM uses Object[] which could lead ClassCastException. That's the reason JVM gives warning during compiling. The annotation is used to by-pass the warning.
20.Catch uncaught exception for a Thread | Thread::setUncaughtExceptionHandler(Thread.UncaughtExceptionHandler eh)
21.Get notification on memory usage | see MemoryPoolMXBean's javadoc 
22.Java is a strict language | Method arguments are passed by value (evaluated first, that's the reason Scala introduced by-name parameters). In a few places however, Java is lazy: &&, ?:, if...else, foo, while, Java 8 streams.
23.Advantages to use anonymous lambda (or method references) | 1, No need to declare type explicitly. 2, Named lambdas are compiled to objects.
24.Add constraints to resource accessing or preventing thread from quitting too early | Semaphore
25.How to prevent assertion from being disabled | Check with a static initializer and quit with RuntimeException (see code)

Continue at P250

(To be continued)

<!-- more -->

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

//12
Dog a = new Dog("A");
void foo(Dog dog) {
  dog = new Dog("foo");
}
foo(a);
//a's name is A, not foo. a is a reference in stack (instead of heap), that points to a Dog instance.
//Inside method foo, dog is a different reference that points to the same Dog instance.
//So after foo, a's name is still A and the reference of dog has been discarded after foo.
//That's the reason of pass-by-value. In this case, it's pass-a-reference-by-value.

//18
ChronoUnit.DAYS.between(d1,d2)
d1.with(TemporalAdjuster.firstDayOfMonth());
d2.with(TemporalAdjuster.next(DayOfWeek.MONDAY));

//25
static {
  boolean assertEnabled = false;
  assert assertEnabled = true;
  if (!assertEnabled) throw new RuntimeException("Assert must be enabled");
}
```
