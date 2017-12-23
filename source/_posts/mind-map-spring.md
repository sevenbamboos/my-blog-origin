---
title: Spring 5
date: 2017-12-10 14:38:14
categories: Programming
---

TODO:

What's the use of java.lang.annotation.Target,Retention? In other words, give a practical view of Java Annotation.

In which case, @Autowired needs to used together with @Qualifier

<!-- more -->

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
17.How to map java.util.Date to java.sql.Date/Time/DateTime in hibernate | @Temporal(TemporalType.DATE)
18.Optimistic lock in hibernate | @Version Don't call setVersion otherwise version check will be disabled. That's one reason to use annotation on field and make get/set private.
19.How to ask hibernate to fetch association eagerly for a query | `left join fetch person.address` 
20.How to support strongly typed criteria API query | Hibernate metamodel generator can generate @StaticMetamodel (JPA 2)
21.How to make transaction cross multiple resources | Use Java Transaction API (JTA) which relies on XA resources to provide distributed transaction implementation.
22.When to use @AssertTrue and when to custom validation | @AssertTrue is inside model to provide inner validation, while custom validation can be reused and injected with external services to do complicated check.
23.Why not to keep JPA EntityManager open for rendering view (on server side) | Not scalable if multiple requests on the page load big data from DB connection. Prefer to use Ajax requests.

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