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
