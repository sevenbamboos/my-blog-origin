---
title: Mind Map of Machine Learning
date: 2017-09-24 11:09:56
categories: Mind Map
---

# Map

Keywords | Comment & Reference
--- | ---
machine learning | [Comment](#machine-learning)
R in Action | [Q&A](#R-in-action)

<!-- more -->

# R in action
Question | Answer
--- | ---
01. How to get a certain row/column of a matrix/dataframe | `data[n,]` for nth row; `data[,n]` for nth column;
02. How to cross-tabulate multiple factors to a table of counts | `table(fac1, fac2, ...)`
03. How to show the structure of a data | `str(data)`
04. How to edit a data | `fix(data)` # the result will be saved to `data` reference.
05. How to configure for `plot` | `plot(x,y,type="b",xlab="x",ylab="y")` ?plot and ?par for details
06. How to choose font when exporting diagram to PDF | `names(pdfFonts())` and then `pdf(file="...", family="font")`
07. How to render math annotation for `plot` | ?plotmath and demo(plotmath)
08. How to transform data frame | `transform(data, sum=c1+c2+c3, mean=(c1+c2+c3)/3)`
09. How to set value to cells of data frame | `performance$insertSort[performance$insertSort == 999] <- NA`
10. How to modify data frame with `within` | data <- within(data,{insert[insert == 999] <- NA })

# machine learning

## road map

Phase 00:

1. Linear algebra
[video](http://open.163.com/special/opencourse/daishu.html)

2. Probability and statistical | All the statistics

Phase 01:

1. *R in Action*

2. Introduction to Statistical Learning

3. Machine Learning in action

4. Andrew Ng's course
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
