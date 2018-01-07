---
title: Domain Model in Practice
date: 2017-12-26 15:42:00
categories: Programming
tags:
- Design Pattern
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

<!-- more -->

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

    List<ID> idOfOwners = mailRepo.mailsFromGroup(groupName).stream()
      .map(mail -> mail.idOfOwner.orElse(null))
      .filter(id -> isAdmin.andThen(ID::isNotEmpty).test(id)));
    
    return personRepo.load(idOfOwners).stream()
      .filter(person -> person.gender == FEMALE);

  }
}
```

So far so good until later product owner asked me to only filter out group admins who work for police station. Then I have the chance to review the method femaleMemberOfGroup. Isn't it too complicated as a service method? And who knows what kind of logic I would be asked to add to this method. It looks like I was working in the old days as a C programmer. All I had were two things: data container (struct) and procedures. Now I'm programming in the same way with an object-oriented language. 

What's more, I'm told to create unit test to cover the method. How to deal with the injected services that are used as dependency of this method? I tend not to use mock objects for its horrible API. Can I do it better?

# A domain model

Domain model is not just a data container as the above models. Here I see a domain model of GroupFemaleMember:
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

      List<ID> idOfOwners = getMails.apply(groupName).stream()
        .map(mail -> mail.idOfOwner.orElse(null))
        .filter(id -> isAdmin.andThen(ID::isNotEmpty).test(id)));
    
      return getPersons.apply(idOfOwners).stream()
        .filter(person -> person.gender == FEMALE); 
    };
  }
}

public final class GroupMemberObject {
  public static GroupMember apply(
    final MailRepository mailRepo,
    final PersonRepository personRepo,
    final String groupName 
  ){
    return new GroupMember(
      name -> mailRepo.load(name),
      name -> mailRepo.mailsFromGroup(name),
      idList -> personRepo.load(idList),
      groupName
    );
  }
}
```

GroupMemberObject knows about how to create the domain model GroupMember from other services. It can be put in the layer above domain model so that there is no dependency issue (high layer can see the low level, not the opposite way). Unit test can rely on GroupMember itself, which has no dependency to external services.

# Unit test of domain model

```
public GroupMemberTest {
  private GroupMember domainModel;

  @Test public void test() {
    ...
    MailGroup group = new ...
    group.addAdmin(admin1).addAdmin(admin2);
    Mail mail1 = group.addMail(address1, malePerson),
         mail2 = group.addMail(address2, femalePerson),
         mail3 = group.addMail(address3, null);
    
    domainModel = new GroupMember(
      _ignored -> group,
      _ignored -> Arrays.asList(mail1, mail2, mail3),
      _ignored -> Arrays.asList(femalePerson)
      group.name
    );

    assertEquals(femalePerson, domainModel.femaleMembers().get().first());
  }
}
```

There is no mock objects since GroupMember is self-contained after the initialization. With the new domain model, the service method is simplified a lot.

# How to use domain model

```
Supplier<List<Person>> femaleMemberOfGroup(String groupName) {
  return GroupMemberObject.apply(mailRepo, personRepo, groupName);
}
```

It doesn't matter whether we need to cover it with unit test since there is almost no logic inside. Also note I adapt the return value from materialized list to a lazy-evaluated supplier, which can postpone the execution of the real service method until the  moment when it's used by application code. A potential advantage is I can add cache support (memorize computational result, maybe as the next topic) or even I can find a way to group multiple callings into one for better performance.

To sum up, domain model is everywhere. It's invisible to the eyes only with CRUD and get/set.

