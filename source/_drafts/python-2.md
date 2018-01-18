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
