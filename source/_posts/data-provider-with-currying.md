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
externalService.consumePerson(long personID, String name, Date birthDate);
externalService.consumePersonAndContact(long personID, String name, List<ContactInfo> contacts);
```
Note external service has a slightly different understanding of person data. What's more, different services ask for different data. How to do this with clean code? 

<!-- more -->

# A striaghtforward implementation

```
// code sample 1
Person person = personRepository.get(personID);
String name = person.fullName();
Date birthDate = person.dateOfBirth().orElse(placeholderOfDate);
externalService.consumePerson(personID, name, birthDate)

// code sample 2
Person person = personRepository.get(personID);
String name = person.fullName();
Date birthDate = person.dateOfBirth().orElse(placeholderOfDate);
List<ContactInfo> contacts = person.contacts().stream().map(ContactInfo::new).collect(Collections.toList());
externalService.consumePersonAndContact(personID, name, contacts);
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
```

And we can introduce a toView method in Person model
```
class Person {
	...
	public PersonView toView() {...}
}
``` 
Someone would argue that PersonView is a model stays in a layer above domain model. So add `toView` to Person would pollute domain model. If that's the case, then we can create a dedicate class to do the transformation between view and model.

But the key point of this pattern is that different external services may ask for different data sets, and therefore we would end up with a lot of view objects. For example in the above example, consumePerson service doesn't need contacts so it's inefficient to construct contacts so we may need another person view without contacts. As application grows with lots of external services, the number of view objects could be too many to maintain.

So how to solve the conflict: on one hand Person model has the detailed info but has no idea how to use it; on the other hand external service knows how to use it but struggles to construct piece of data from domain model. 

# Currying


