---
title: Data Provider with Currying
date: 2017-12-25 11:28:22
categories: Programming
tags:
- Clean Code
---

# What is the problem

Suppose we have a Person model like this:
```
class Person {
	private ID id;
	private PersonName name;
	private BirthDate dateOfBirth;
	private TransactionalDate lastModified;
	private List<Contact> contacts;
	...
}
```
Something additional about the way of modeling. I could create the same Person in this way:
```
class Person {
	private long id;
	private String name;
	private Date dateOfBirth;
	private Date lastModified;
	...
}
```
The benefit of using ID, PersonName and BirthDate instead of long, String and Date respectively is that:
1. These basic types could be very likely reused in other models. Having a separate definition gives them a place to hold their own business logic other than the generic type shipped with language library. For example, ID is a numeric long greater than zero (due to the fact of incremental primary key in database). ID could have a property 'Empty' to indicate the data has not been persisted yet, which is better than using expression `if (id > 0)` that leaks too much technical details. And for BirthDate, we can apply a constraint on it due to the limit of human being's age; while for TransactionalDate, it could have a default value as the current date, which represents the timing of a business transaction.
2. Less possibility of using a wrong field because most fields have a different type so that compiler can easily detect such error. That's where the misleading practice, 'Don't use the same type for different fields when modeling a business object', comes from.

And in application code we need to send person data to an external service like this:
```
externalService.consumePerson(long personID, String name, Date birthDate, ...);
externalService.consumePersonAndContact(long personID, String name, List<ContactInfo> contacts, ...);
```
Note external service has a slightly different understanding of person data. What's more, different services ask for different data. How to do this with clean code? 

<!-- more -->

# A straightforward implementation

```
// code sample 1
Person person = personRepository.get(personID);
String name = person.fullName();
Date birthDate = person.dateOfBirth().orElse(placeholderOfDate);
externalService.consumePerson(personID, name, birthDate, ...)

// code sample 2
Person person = personRepository.get(personID);
String name = person.fullName();
Date birthDate = person.dateOfBirth().orElse(placeholderOfDate);
List<ContactInfo> contacts = person.contacts().stream().map(ContactInfo::new).collect(Collections.toList());
externalService.consumePersonAndContact(personID, name, contacts, ...);
```
The drawback with the code is that the application knows too much details about Person model and has duplicated code in both samples when invoking different external services. How can I improve this?

# Data provider pattern

One way is to define a view object for external services to consume. Someone calls this 'Data Transfer Object'.
```
interface PersonView {
	long id();
	String name();
	Date birthDate();
	List<ContactInfo> contacts();
}

class Person {
	...
	public PersonView toView() {...}
}
```

Someone would argue that PersonView is a model stays in a layer above domain model. So add `toView` to Person would pollute domain model. If that's the case, then we can create a dedicate class to do the transformation between view and model.

But the key point of this pattern is that different external services may ask for different data sets, and therefore we would end up with a lot of view objects. For example in the above example, consumePerson service doesn't need contacts so it's inefficient to construct contacts so we may need another person view without contacts. As application grows with lots of external services, the number of view objects could be too many to maintain.

So how to solve the conflict: on one hand Person model has the detailed info but has no idea how to use it; on the other hand external service knows how to use it but struggles to construct piece of data from domain model. 

# Currying

The argument binding in Javascript gives me the inspiration. If I can pass to Person a function that Person can partially bind detailed info to function arguments and return the function back so that application code can continue to execute the function with additional arguments. 

It should work unless I can solve the limit that Java (or C# or Scala or other strong typed languages) doesn't allow free placeholders for function parameters. For example, to bind ID, name and birth date, I may need a `TripleFunction<Long, String, Date>`, while for the additional contact it might be a `FourthFunction<Long, String, Date, List<ContactInfo>>`. Java 8 only has Function<T,R> and BiFunction<T1,T2,R> in its `java.util.function`, which means I need to create more XXXFunctions by my own.

The solution is to use currying to avoid define so many tedious function types. In theory, currying can support as long as a parameter list could be. Here are the currying functions:
```
Function<ID, Function<PersonName, Function<BirthDate, Runnable>>> idNameBirthDayFunc;
Function<ID, Function<PersonName, Function<BirthDate, Function<List<Contact>, Runnable>>> idNameBirthDayContactsFunc;
```
Lack of powerful type inference, Java's type declaration seems horrible, but bear with me for the moment and I'll improve it later. The important note is that I use domain types (e.g. ID and Contact) instead of general types (e.g. long and ContactInfo) is because domain model doesn't need to know about how it is used by external services. It just binds what it has and it's the responsibility of application (or external services) to do necessary data transformation to use it. It's critical to make domain model stable and versatile. Another note is that I use Runnable to represent the idea of a functional interface that accept nothing and after the execution return nothing. Someone (including some IDEs) may complain about the use ofRunnable in non-multi thread. If that's the case, rename it to `@FunctionalInterface interface Executable { void exec() }`.

Then I add two methods to Person model:
```
Runnable bind(idNameBirthDayFunc) {...}
Runnable bind(idNameBirthDayContactsFunc) {...}
```
Wait. It's too long for the function types so that I tempt not to write them again. Is there a way like type alias available in Java? Sure.
```
public static interface IDNameBirthDayFunc extends Function<ID, ...> {}
public static interface IDNameBirthDayContactsFunc extends Function<ID, ...> {}
```
I define them inside Person as part of the model, which makes them look like delegates in C#. Now I can use the function types much easier. Here is the completed code of bind method: 
```
Runnable bind(IDNameBirthDayFunc<R> func) {
	return func.apply(id).apply(name).apply(dateOfBirth);
}

Runnable bind(IDNameBirthDayContactsFunc<R> func) {
	return func.apply(id).apply(name).apply(dateOfBirth).apply(contacts);
}
```
Next, let's see how to use them in application code.

# How to consume the currying function

```
// code sample 1.2
Person person = personRepository.get(personID);
IDNameBirthDayFunc func = _ignoredID -> name -> birthDay -> () -> {
	String fullName = name.fullName();
	Date birthDate = birthDay.orElse(placeholderOfDate);
	externalService.consumePerson(personID, fullName, birthDate, ...);
};
person.bind(func).run();

// code sample 2.2
Person person = personRepository.get(personID);
IDNameBirthDayFunc func = _ignoredID -> name -> birthDay -> contacts -> () -> {
	String fullName = name.fullName();
	Date birthDate = birthDay.orElse(placeholderOfDate);
	List<ContactInfo> contactInfos = contacts.stream().map(ContactInfo::new).collect(Collections.toList());
	externalService.consumePerson(personID, fullName, birthDate, contactInfos, ...);
};
person.bind(func).run();
```
Wait again. The number of lines of the 'improved' code is more than before. Does it make sense to make so much effects with the result like this? It depends.

First, if domain model can have part of data conversion logic like full name or birthday (not including ContactInfo because domain model has no dependency to it and doesn't know it), then these logic can be put in bind methods and we don't need to repeat it in every place that use it. Then it can save us some duplicated code.

Second, for computational property, the currying solution provides the flexibility. For example, if external service needs person age instead of birthday, then Person model doesn't need to be aware of it and just exposes birthday and let application code to compute the age (with local timezone). Someone would argue that domain model can include computational property but it doesn't always work. The main idea is that with currying solution, application code doesn't know too much about the internal details of domain model, just the enough information provided from the currying functions. And compared with view objects, currying functions are much lightweight. Actually, if Java compiler supports a better type interference (for example, use `var` in variable deceleration), I don't need those currying functions because I can create them on the fly.

In conclusion, introducing currying functions breaks the strong coupling between domain model and application code. 

以上.
