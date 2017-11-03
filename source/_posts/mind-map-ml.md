---
title: Mind Map of Machine Learning
date: 2017-09-24 11:09:56
categories: Mind Map
---

# Map

Keywords | Comment & Reference
--- | ---
machine learning | [road map](#machine-learning)
R | [r-in-action](#R-in-action)
Python | [basic](#Python-Basic), [tips](#Python-Tips)
Statistics | [naked statistics](#naked-statistics)
Math | [linear algebra intro](#introduction-to-linear-algebra)

<!-- more -->

# naked statistics
Question | Answer
--- | ---
01.Is it a contradiction between gambler's fallacy and reversion to the mean | For each (next) independent event, the probability remains the same, which explains gambler's fallacy. In the long term, however, the reversion of the result to the mean is a trend. So it is not a contradiction.
02.Shall we hold the stock when the CEO appears on Businessweek | According to the rule of reversion to the mean, we'd better sell it.
03.1 of 3 boxes has a treasure. Host knows which one it is. After you choose one, host will open another that is empty. Shall we change the mind to choose another | Yes, the probability will become from 1/3 to 2/3.
04.Central limit theorem | Sample means will be distributed as a normal distribution around the overall mean.
05.Standard error | Standard deviation of sample data. se = \frac{sd}{\sqrt n} (see quote 01)
06.Sample size in central limit theorem | Over 30 as a rule of thumb
06.A common threshold to reject a null hypothesis in regression analysis | 5%. 68% within one standard deviation, 95% within two and 99% within three. Here 95% is where 5% comes from (100%-95%)
07.Standard error for probability | se = \sqrt {\frac {p(1-p)} {n}} (see quote 02)

```
// definition
se = standard error
n = sample size
sd = standard deviation
p = probability

// quote 01
se = \frac{sd}{\sqrt n}

// quote 02
se = \sqrt {\frac {p(1-p)} {n}}
``` 

# Python Tips
For Anaconda distribution, this (mirror)[https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/] could be faster than the official.
(numpy tutorial)[https://docs.scipy.org/doc/numpy-dev/user/quickstart.html]


# Python Basic
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
11.Flexible number of named parameters | `def foo(**user_info)` // Note the call side doesn't need to create a dictionary to use user_info (see source code 01)
12.Import a function with another name from a module | `from a_module import function_a as function_b`
13.Arrange function parameters in multiple lines | use two TAB to make parameters look different from function body (see source code 02) 
14.Call methods of super class | `super().__init__(...)`
15.Open a file without closing it explicitly | `with open('file_name') as file_object:`
16.Loop through file object line by line | `for line in file_object:`
17.Exception handling | try-except-else-finally block. Use pass in except branch to fail silently.
18.Is there concept of Option | No. None is same as null.
19.Check if variable is None | `a is None` // is not is to check the opposite
20.Unpack sequence | `a,b,*_ = ("foo","bar",1,2)` // _ => [1,2]
21.Performance of list insert, pop and in | For non-last element, it's O(n) while n is the size of the list
22.For loop with index | `for i,value in enumerate(seq):`
23.Convert a list of rows into a list of columns | `zip(*seq)` //see source code 03
24.Objects can be put in dictionary as key | number, string, tuple (immutable) or anything else has fixed value of `hash(obj)`
25.Nested for comprehension | `[v2 for v1 in s1 if ... for v2 in v1 if ...]`
26.Variable scope | There are two scopes: local and global. class, function can create nested scope, but not if/for/while.
27.Currying | `from functools import partial` and `partial(foo, some_variable)`
28.Generator in for comprehension | `(x for x in ... if ...)` or use `yield` in generator function
29.Anonymous function | `lambda x: x[0]`
30.Generator from itertools | groupby, combinations, product, permutations, etc.
31.Catch multiple errors | `except (TypeError, ValueError):`

```
//01
def foo(**user_info):
    res = {}
    for k, v in user_info.items():
        res[k] = v
    return res


user = foo(lastName="Foo", firstName="Bar")
for k, v in user.items():
    print("{} -> {}".format(k, v))

//02
def foo(
        param1,
        param2,
        param3):
    // function body

//03
names = [("Tony","Zhang"),("Anri","Liu"),("Charry","Y")]
fns, lns = zip(*names)
fns // ("Tony","Anri","Charry")
lns // ("Zhang","Liu","Y")

```

# R in action
Question | Answer
--- | ---
01.How to get a certain row/column of a matrix/dataframe | `data[n,]` for nth row; `data[,n]` for nth column;
02.How to cross-tabulate multiple factors to a table of counts | `table(fac1, fac2, ...)`
03.How to show the structure of a data | `str(data)`
04.How to edit a data | `fix(data)` # the result will be saved to `data` reference.
05.How to configure for `plot` | `plot(x,y,type="b",xlab="x",ylab="y")` ?plot and ?par for details
06.How to choose font when exporting diagram to PDF | `names(pdfFonts())` and then `pdf(file="...", family="font")`
07.How to render math annotation for `plot` | ?plotmath and demo(plotmath)
08.How to transform data frame | `transform(data, sum=c1+c2+c3, mean=(c1+c2+c3)/3)`
09.How to set value to cells of data frame | `performance$insertSort[performance$insertSort == 999] <- NA`
10.How to modify data frame with `within` | data <- within(data,{insert[insert == 999] <- NA })
11.How to remove missing data (when it's not too much) | `data <- na.omit(data)`
12.How to convert date | as.Date, date() and Sys.Date()
13.How to sort data | `order(gender, -age)`
14.How to combine data frames | cbind(), rbind(), merge(frame1,frame2,by=c("id"))

# machine learning

## road map

### Read
01. Python crash course
02. Naked statistics
03. *Introduction to linear algebra*

Phase 00:

1. Linear algebra
[video](http://open.163.com/special/opencourse/daishu.html)

2. Probability and statistical | All the statistics

Phase 01:

00. Python crash course

01. *Python for Data Analysis*

10. *R in Action*

12. Introduction to Statistical Learning

13. Machine Learning in action

14. Andrew Ng's course
[video](http://open.163.com/special/opencourse/machinelearning.html)
[materials](http://cs229.stanford.edu/materials.html)

5. Python...

Phase 02.a:

? Patten Recognition and Machine Learning

? Elements of Statistical Learning

? Deep Learning

Phase 02.b:

Hadoop, Spark, ...

## reference
[scikit][1]


[1]: http://scikit-learn.org 
