---
title: Python for Java Programmer - 1
date: 2018-01-18 14:09:56
categories: Programming
tags:
- Python
---

# Tips of Anaconda installation
For Anaconda distribution, this (mirror)[https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/] could be faster than the official.
(numpy tutorial)[https://docs.scipy.org/doc/numpy-dev/user/quickstart.html]

matrix with Jupyter
```
%matplotlib inline
import sympy as sympy
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sbn
from scipy import *

A = np.array(([.8,.3],[.2,.7]))
A.dot(x1)
```

# Language Basic
Question | Answer
--- | ---
01.Access the last element of a list | `aList[-1]` // it can be interpreted as `aList[len(aList)-1]`
02.Remove an element from a list | `previousLastOne = aList.pop()` or `del aList[-1]` The first one can get the removed one.
03.Is it accessible for a (loop) variable after the loop (e.g. for ele in elements:) | Sadly, it is (e.g. ele). How to get rid of it? (`del ele`, ugly workaround)
04.How to turn a range object to a list | `list(range(1,5))` // range() returns a range object (iterable) and passed to list() to become a list
05.How to slice a list | `aList[-3:]` // obviously here : needs specific syntax support
06.Find if an element exists in a list/string | `element in aList` or `sub_string in aString` // it returns a boolean, a syntax keyword
07.How to check empty string/list | `if aList:` or `if aString:` // empty string/list is false in for and if
08.Loop through a dictionary | `for k, v in aDict.items()`
09.Provide a description to a function/class/module | Put `"""..."""` (docstring) below a function/class or at the beginning of a module
10.Flexible number of function parameters | `def foo(p, *pa):` // put *pa after positioned parameters
11.Flexible number of named parameters | `def foo(**user_info)` // Note the call side doesn't need to create a dictionary to use user_info (see source code)
12.Import a function with another name from a module | `from a_module import function_a as function_b`
13.Arrange function parameters in multiple lines | use two TAB to make parameters look different from function body (see source code) 
14.Call methods of super class | `super().__init__(...)`
15.Open a file without closing it explicitly | `with open('file_name') as file_object:`
16.Loop through file object line by line | `for line in file_object:`
17.Exception handling | try-except-else-finally block. Use pass in except branch to fail silently.
18.Is there concept of Option | No. None is same as null.
19.Check if variable is None | `a is None` // is not is to check the opposite
20.Unpack sequence | `a,b,*_ = ("foo","bar",1,2)` // _ => [1,2]
21.Performance of list insert, pop and in | For non-last element, it's O(n) while n is the size of the list
22.For loop with index | `for i,value in enumerate(seq):`
23.Convert a list of rows into a list of columns | `zip(*seq)` //see source code
24.Objects can be put in dictionary as key | number, string, tuple (immutable) or anything else has fixed value of `hash(obj)`
25.Nested for comprehension | `[v2 for v1 in s1 if ... for v2 in v1 if ...]`
26.Variable scope | There are two scopes: local and global. class, function can create nested scope, but not if/for/while.
27.Currying | `from functools import partial` and `partial(foo, some_variable)`
28.Generator in for comprehension | `(x for x in ... if ...)` or use `yield` in generator function
29.Anonymous function | `lambda x: x[0]`
30.Generator from itertools | groupby, combinations, product, permutations, etc.
31.Catch multiple errors | `except (TypeError, ValueError):`

```
// 11
def foo(**user_info):
    res = {}
    for k, v in user_info.items():
        res[k] = v
    return res


user = foo(lastName="Foo", firstName="Bar")
for k, v in user.items():
    print("{} -> {}".format(k, v))

// 13
def foo(
        param1,
        param2,
        param3):
    // function body

// 23
names = [("Tony","Zhang"),("Anri","Liu"),("Charry","Y")]
fns, lns = zip(*names)
fns // ("Tony","Anri","Charry")
lns // ("Zhang","Liu","Y")

```

## reference
[scikit][1]


[1]: http://scikit-learn.org 
