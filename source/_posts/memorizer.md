---
title: Memorizer
date: 2017-12-27 16:44:00
categories: Programming
tags:
- Clean Code
---

# What is the problem

Continue with the last topic about domain model, there is a performance problem. See the code:

```
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

```

Given the same groupName, if femaleMembers is invoked by more than one place within the same transaction, it will trigger the query each time even when the result is almost identical. So how to improve the performance?

<!-- more -->

# Memorizer pattern

It's tempted to introduce a cache (groupName -> List<Person>) for the domain model. But it means every time there is a requirement to memorize computational result, I need to add a dedicated cache. What about a cache with global access and management? It's clumsy and also not test friendly. The cache should be as near as possible to the actual computation and as generic as possible to be reused for other scenarios. See the following memorizer pattern (adapted from the pattern with the same name from 'Functional programming in Java'):
```
public class Memoizer<T, U> {

	private final Map<T, U> cache = new ConcurrentHashMap<>();

	private Memoizer() {}

	public Function<T, U> memoize(Function<T, U> function) {
			return input -> cache.computeIfAbsent(input, function::apply);
	}
    
	public Supplier<U> memoize(T key, Supplier<U> supplier) {
	  	return toSupplier(key, memoize(toFunc(supplier)));
	}
	    
	private Function<T, U> toFunc(Supplier<U> supplier) {
	   	return _ignored -> supplier.get();
	}
	    
	private Supplier<U> toSupplier(T t, Function<T, U> func) {
		return () -> func.apply(t);
	}    
}
```

It supports both `java.util.function.Function` and `java.util.function.Supplier`. To help test the result, I also introduce a helper counter and doMemorize has been adapted:
```
	private int executedCount = 0;
	private int count = 0;  

	public Function<T, U> memoize(Function<T, U> function) {
		return input -> {
			++count;
			return cache.computeIfAbsent(input, k -> {
				++executedCount;
				return function.apply(k);
			});
		};
	}

    public MemoizerCounter counter() {
	    return new MemoizerCounter() {
				@Override public int executed() { return executedCount; }
				@Override public int total() { return count; }
	    };
    } 

```

# How to use the pattern

For the above domain model, to use the memorizer pattern, first is to add a field:
```
private final Memoizer<String, List<Person>> memoizer = new Memoizer<>();
```

Note the type of memorizer indicates how the cache is organized (from group name to a list of members). And femaleMembers is adapted:

```
  public Supplier<List<Person>> femaleMembers() {

    return memoizer.memoize(groupName, () -> {

      List<ID> idOfAdmins = loadGroup.apply(groupName).admin.stream()
        .map(admin -> admin.id);

      Predicate<ID> isAdmin = id -> 
        idOfAdmins.stream().anyMatch(adminId -> adminId != id); 

      List<ID> idOfOwners = getMails.apply(groupName).stream()
        .map(mail -> mail.idOfOwner.orElse(null))
        .filter(id -> isAdmin.andThen(ID::isNotEmpty).test(id)));
    
      return getPersons.apply(idOfOwners).stream()
        .filter(person -> person.gender == FEMALE); 
    });
  }

```

It's almost the same as before, just being wrapped in memoizer's doMemoize method. The advantage of the memorizer pattern is that different memorizer instances have no correlation and therefore no need to worry about memory leak or stale objects in cache. On the other hand, the memorizer stays close with each model and is test-friendly.

