---
title: Python for Java Programmer - 2
date: 2018-01-20 10:19:15
categories: Programming
tags:
- Python
---

# Intermediate Level
Question | Answer
--- | ---
01.Create simple class | collections.namedtuple (see code)
02.`__str__` VS `__repr__` | str() calls the latter when the former is missing. 
03.Why exclude the last item when sclicing | One reason is `lst[:n] + lst[n:] == lst` 
04.Variables created inside listcomp | They exist after listcomp
05.Application of score to grade | `bisect.bisect` (see code)
06.Counterpart of `Map::computeIfAbsent` | `dict.setdefault`
07.Tricky way to handle missing key for a dict | `__getitem__` fallback to `__missing__`. So implement the latter accordingly.
08.Check the existing of an attribute in instance method | `hasattr(self, 'xxx')`
09.Closure and nested function | Local variables in outer function are free variables for inner function. When inner function returns as return value, free variables captured as closure.
10.Local variables in the current scope | `locals()` built-in function to return a dict
11.is operator VS == | `is` is faster in that it compares id, while == could be overrided in user type with `__eq__` 
12.Mutable object used as default value of function parameter | Default value evaluated once when function is defined. So the instance of default value is shared among methods from different instances and therefore is dangerous.
13.Things can keep unexpected references so as to prevent objects from destroying | _ and Traceback object. 
14.Quickly compare multiple variables | `tuple(this) == tuple(other)`
15.Another way of hash code generation other than odd prime 31 and 17 | xor

```
# 01
Name = collections.namedtuple('Name', ['first','last'])
aName = Name('John', 'Doe')
# first = John, last = Doe

# 05
def score2grade(score, ranges=[60,70,80,90],grades=[E,D,C,B,A]):
	i = bisect.bisect(ranges,score)
	return grades[i]

```

## reference
Fluent Python 2015.7
