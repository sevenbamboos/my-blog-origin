---
title: Domain Model in Practice
date: 2017-12-26 15:42:00
categories: Programming
tags:
- Clean Code
---

# What is the problem

Suppose we have domain models like this:
```
class Person {
  ID id;
  PersonName name;
  Gender gender;
}

class Mail {
  Address address;
  MailGroup group;
  ID? idOfOwner;
}

class MailGroup {
  String name;
  Person[] admin;
}
```

One day, product owner asked me to do a story with the following requirement:
> Given a mail group, list female owners of mail addresses in this group. The list should exclude the admin of this mail group for whatever reason.

# A straightforward implementation

I check the code base and find the following repositories for mail and person:
```
@service class MailRepository {
  List<Mail> mailsFromGroup(String groupName) {...}
  MailGroup load(String name) {...}
}

@service class PersonRepository {
  List<Person> load(List<ID> idList) {...}
} 
```

Here is the a new method in repository of mail group:
```
@service class MailGroupRepository {
  @Inject MailRepository mailRepo;
  @Inject PersonRepository personRepo;

  List<Person> femaleMemberOfGroup(String groupName) {
    
    List<ID> idOfAdmins = mailRepo.load(groupName).admin.stream()
      .map(admin -> admin.id);

    Predicate<ID> isAdmin = id -> 
      idOfAdmins.stream().anyMatch(adminId -> adminId != id); 

    List<ID> idOfOwners = mailRepo.mailsFromGroup(goupName).stream()
      .map(mail -> mail.idOfOwner.orElse(null))
      .filter(id -> isAdmin.andThen(ID::isNotEmpty).test(id)));
    
    return personRepo.load(idOfOwners).stream()
      .filter(person -> person.gender == FEMALE);

  }
}
```

So far so good until later product owner asked me to only filter out group admins who work for police station. Then I have the chance to review the method femaleMemberOfGroup. Isn't it too complicated as a service method? And who knows what kind of logic I would be asked to add to this method. It looks like I was working in the old days as a C programmer. All I had were two things: data container (struct) and procedures. Now I'm programming in the same way with an object-oriented language. 

What's more, I'm told to create unit test to cover the method. How to deal with the injected services that are used as dependency of this method? I tend not to use mock objects for its horrible API. Can I do it better?

# What is domain model

Domain model is not just a data container as the above models. Here I see another domain model of GroupFemaleMember:
```
class GroupMember {

  // type alias
  static interface GroupAdminsFunc extends Function<String, MailGroup> {}
  static interface GroupMailsFunc extends Function<String, List<Mail>> {}
  static interface PersonsFunc extends Function<List<ID>, List<Person>> {}

  // fields
  private final GroupFunc loadGroup;
  private final GroupMailsFunc getMails;
  private final PersonsFunc getPersons;
  private final String groupName;

  // constructor ...

  // business method
  public Supplier<List<Person>> femaleMembers() {

    return () -> {

      List<ID> idOfAdmins = loadGroup.apply(groupName).admin.stream()
        .map(admin -> admin.id);

      Predicate<ID> isAdmin = id -> 
        idOfAdmins.stream().anyMatch(adminId -> adminId != id); 

      List<ID> idOfOwners = getMails.apply(goupName).stream()
        .map(mail -> mail.idOfOwner.orElse(null))
        .filter(id -> isAdmin.andThen(ID::isNotEmpty).test(id)));
    
      return getPersons.apply(idOfOwners).stream()
        .filter(person -> person.gender == FEMALE); 
    };
  }
}

public GroupMemberFactory {
  public static GroupMember apply(){}
}
```

# Benefit of domain model