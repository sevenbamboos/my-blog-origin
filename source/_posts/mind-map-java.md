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
12.Pass-by-value or pass-by-reference | Java is always pass-by-value. (see code)
13."foo" VS new String("foo") | The former is in string constant pool, while the latter is a normal string object. (see [this](http://www.thejavageek.com/2013/06/19/the-string-constant-pool/)) 
14.Comparator VS comparable | The former is a util interface comparing two objects, while the latter compares another object with itself, which means it is implemented by compared types while comparator is used by sorting functions.
15.Why Object::hashCode() is native | Performance concern because it looks for an integer representation of an object reference on the heap. 
16.What's the benefit of Collections::copy() over collection constructor or addAll | It doesn't involve reallocation but instead trigger out-of-index error if happened.
17.Return value for high-order function | For functional interface, return a lambda (instead of an anonymous class).
18.java.time package since Java 8 | LocalDate|Time|DateTime, Year, MonthDay, DayOfWeek. Also refer to TemporalAccessor for use suggestion (see code)

Continue at P250

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
09.How to register shutdown hook | Runtime::addShutdownHook(Thread) Hooks are invoked when the last non-daemon thread exit.
10.How to notify a non-critical business logic in Spring | ApplicationEvent works synchronously. For long-running process, JMS is more suitable.
11.How to switch different environments for build tools (e.g. Maven) | @Profile and @ActiveProfiles
12.What's the use of afterThrowing in Spring AOP | Adjust exception hierarchy, centralize exception logging and help debugging live application without modify application code. 
13.How to limit AOP point-cut to the situation when it is called by a specific object | ControlFlowPointcut Be careful about performance impact.
14.How to do object modification detection | AOP on set* methods and compare new value with old value (from corresponding get* methods).
15.What is hibernate.max_fetch_depth | Person.address.city.country, if the depth is 2 then getCountry() will trigger another query.
16.How to get nice sql generated by hibernate | hibernate.format_sql
17.How to map java.util.Date to java.sql.Date/Time/DateTime in hibernate | @Temporal(TemporalType.DATE|TIME|DATETIME)
18.Optimistic lock in hibernate | @Version Don't call setVersion otherwise version check will be disabled. That's one reason to use annotation on field and make get/set private.
19.How to ask hibernate to fetch association eagerly for a query | `left join fetch person.address` 
20.How to support strongly typed criteria API query | Hibernate metamodel generator can generate @StaticMetamodel (JPA 2)
21.How to make transaction cross multiple resources | Use Java Transaction API (JTA) which relies on XA resources to provide distributed transaction implementation.
22.When to use @AssertTrue and when to custom validation | @AssertTrue is inside model to provide inner validation, while custom validation can be reused and injected with external services to do complicated check.

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
