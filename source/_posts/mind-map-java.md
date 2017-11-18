---
title: Mind Map for Java
date: 2017-11-10 15:38:14
categories: Mind Map
---

# Category

Section | Contents
--- | ---
Basic | [Basic](#Basic)
Spring | [Spring](#Spring)

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

# Spring 
Question | Answer
--- | ---
01.JDK dynamic proxy VS CGlib (, javassist, etc.) | Dynamic proxy applies to interface, while CGlib to sub-class. (see code)
02.Whether to put configuration option in business interface | It depends on whether it makes sense to all implementations. If not, define a separate interface for configuration.
03.Dependency lookup VS injection | lookup is active and tends to have more code but (personally) easier to take full control (for test mock and run in standalone mode), while injection is passive and less code.
04.What's the bad side of field injection | Hard to inject dependency manually (for test) because of the private field; Rely on container's injection.
05.When to use @Resource instead of @Autowired | If the dependency is in a collection`<type>` (array,list,set,map), @Autowired will look for all beans of the contained type instead of the bean of the collection itself.
06.In case for lookup method injection, what's the difference between annotation and XML | Annotation needs a non-abstract empty method implementation, while XML configuration can use an abstract method.
07.Lookup and replaced method | Lookup is used when the dependency has a different lifecycle (e.g. non-singleton) than the host (e.g. globally singleton). So the dependency can be different instances on every time it's invoked. Replaced method is a class implements MethodReplacer to replace a method implementation of a delegated object.
08.What's the use of bean name alias | On maintenance to replace existing dependencies with a new name.

TODO:

What's the use of java.lang.annotation.Target,Retention? In other words, give a practical view of Java Annotation.

In which case, @Autowired needs to used together with @Qualifier

```
// 01
interface Foo ...
class FooImpl implements Foo ...
class DynamicProxy implements InvocationHandler {
  public Object createDynamicProxy() {  
    return Proxy.newProxyInstance(...);  
  }
  @Override public Object invoke(...) throws Throwable {
    // before ...
    Object result = method.invoke(this.target, args); 
    // after ...
    return result;  
  }  
}
```
