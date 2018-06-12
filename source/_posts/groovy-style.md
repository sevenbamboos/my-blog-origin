---
title: Single Script Pattern in Groovy and Style Suggestion
categories: Programming
date: 2018-06-09 20:52:00
tags:
---


# Introduction
In some cases, Groovy scripts are loaded (by script engine of the hosted application server) as a single piece and have no dependency to other shared libraries. In this situation, the scripts need to be self-contained with both application code and support code. How to split these two parts of code and how to make use of Groovy's dynamic language features is the main topic of this blog. There are also some suggestions of what is good code style about Groovy (for Java programmers).

<!-- more -->

# Single Script Pattern
## Purpose
To encourage code reuse, one way is to share support code among application codes. The shared code can be business model or common utilities like the following:
```
App 1 --> Model <-- App 2
App 1 --> Utility <-- App 2
```
The idea is to make application code as thin as possible. The reason is application code is more likely to be affected by requirements and external systems, and therefore needs more effects of maintenance. On the other hand, if support code can be defined as algebraic data type (ADT), it will have good properties to be stable and easy to be reused by different application codes.

In some situations however, we are not allowed to have a physically shared support code (due to the limit of class loader or name space). To arrange the source code as the above structure, we can make sure of Single Script Pattern. The word script is not necessary to be a script language, although script languages often support to have everything in a single script. The idea of this pattern is to have a self-contained code piece with good structure. Here I use Groovy to explain the pattern partly because Groovy's neat syntax and superb support of Meta-Programing.

## Details
Before going to the details, I first explain what Meta-Programming I will use in this pattern. I try to avoid the terminology of dynamic-typing, which I think is oftentimes confusing. Groovy is not a programming language without type. It just supports optional type declaration and has type inference. It also supports duck typing, but more importantly, the point of dynamic-typing is not because the type can be omitted, but because the method call will be dispatched at runtime according to the binding. For the sake of this, I stick in Meta-Programming.

Groovy supports a few ways of Meta-Programming. Please refer to the followed parts for other options. Here I use Extension Module, which is well-known by C# programmers as extension methods.

The overall structure of the script is like followed:
```
final class XXXModule {
  static businessLogic1 ( Model1 self ) { ... }
  static businessLogic2 ( Model2 self, Param1 p1, Param2 p2 ) {
    ...
    _internalHelperMethod(...)
    ...
  }
  static helperMethod ( String self ) { ... }
  private static _internalHelperMethod (...) { ... } 
}

use (XXXModule) {
  def model1 = ...
  model1.businessLogic1()
  def model2 = ...
  model2.businessLogic2(param1, param2)

  def text = "some-text"
  println text.helperMethod()
}
```

The advantage of Single Script Pattern is:
1. It can extend methods for more than one type.
2. Extended methods are ONLY available in the `use` block, without the pollution of the whole name space.
3. Extended methods improve the readability and can be served in the form of DSL.
4. Application code is split from support code.
The first two points are also the benefit from Extension Module.

## Example
Suppose in an online shopping application, we have models:
Order has more than one OrderItem.
OrderItem consists of a Product, amount of the product, and the price.
OrderItem can also have a customer comment.
```
@Canonical class Customer {
    String name
}

@Canonical class Product {
    String code
    def price = 0.0
}

@Canonical class OrderItem {
    Product product
    def price = 0.0, amount = 0.0
    def comments = []
    def totalPrice() { price * amount }
}

@Canonical class Order {
    Customer customer
    def items = []

    def totalPrice() {
        items.inject(0.0) { c, a -> c + a }
    }
}
```

To present a page of order history from a certain customer, we need to make a list of orders with an additional property of whether there is any OrderItem without a comment.
```
final class OrderHistorySupport {
    
    static withoutComment(Order self) {
        self.items.any { ! (it as OrderItem).comments }
    }
    
    static makeOutputList(List<Tuple2> self) {
        self
          .withIndex()
          .flatten { Tuple2<Tuple2> item ->
            new Tuple(item[1], item[0][1], item[0][0])
          }
    }
}

use(OrderHistorySupport) {
    def orders = ...

    orders
      .collect { new Tuple2(it, (it as Order).withoutComment()) }
      .makeOutputList()
      .generateHTML()
    ...
}
```


# Groovy Style
## Elvis Operator
```
String name = node.getName() == null ? 'DEFAULT_NAME' : node.getName()
// node.getName() appears twice, it is a duplication
// although it could be optimized by compiler, we still need a better way
// to reduce typing and improve the readability.

String name = node.getName() ?: 'DEFAULT_NAME'
```

## Spread and GPath
```
def listOfNodes = [
  ['id': 1, 'label': 'node1'],
  ['id': 2, 'label': 'node2'],
  ['id': 3]
]

assert listOfNodes.label == ['node1', 'node2']
// GPath collects only non-null values

assert listOfNodes*.label == ['node1', 'node2', null]
// Spread operator caters for null values, and the equivalent notation is
assert listOfNodes*.label == listOfNodes.collect { it?.label }
// I just wonder when Groovy would rename collect to map
```

## Why Closure is a Better Solution for Template Method
Suppose we have an (expensive) resource to handle

```
def someResource = retrieveAndOpenExternalResource()
someResource.doSomething()
someResource.close()
```

But what about something wrong inside doSomething? In Java (7+), it looks like:

```
try (SomeResource someResource = retrieveAndOpenExternalResource()) {
  someResource.doSomething();
} catch (SomeResourceException ex) {
  ...
}
```

Groovy doesn't support try-with-resource syntax, but provides a better solution without the introduction of a new syntax:
```
retrieveAndOpenExternalResource().withResource {
  it.doSomething()
}
```
withResource takes care of closing the resource even in case of exception.

## String Enhancement
```
println 'http://google.com'.toURL().text // the whole HTML of Google's front page
println 'http://google.com'[7..12, 14..-1] // google com
```

## POJO Enhancement
```
@EqualsAndHashCode class AClass { ... }
@ToString class AClass { ... }
@TupleConstructor class Name {
  String firstName
  String lastName
}
def aName = new Name("John", "Doe")

@Canonical class AClass { ... } // same as the above three annotations
```

## Regex Pattern Matching
Notice the difference between `==~` and `=~`
```
boolean isEmail = mail ==~ /[\w.]+@[\w.]+/
java.util.regex.Matcher matcher = mail =~ /[\w.]+@[\w.]+/
```

## Meta-Programming

### Meta-Class
Add helper methods to commonly used classes once and for all:
```
HttpSession.metaClass.getAt = { delegate.getAttribute(it) }
HttpSession.metaClass.putAt = { key, value -> delegate.setAttribute(key, value) }
// use delegate which is the owner object of the closure (not this)

def session = request.session
session['token'] = 'abc123'
def aToken = session['token']
```

### Categories
Another way is to use category, which has two advantages: one is the extension methods are more natural and similar to normal method definitions (can use `this`), and the other is it can control the scope of these extension methods
```
@Category(HttpSession)
class MyEnhancedHttpSessionCategory {
  def getAt(String key) { this.getAttribute(key) }
  def putAt(String key, Object value) { this.setAttribute(key, value) }
}

use(MyEnhancedHttpSessionCategory) {
  def session = request.session
  session['token'] = 'abc123'
  def aToken = session['token']
}
```

### Delegation
```
class LegacyModel {
  def foo() { ... }
}

class Model {
  @Delegate final LegacyModel legacyModel
  Model() { legacyModel = new LegacyModel() }
  def bar() { ... }
}

def aModel = new Model()
aModel.bar()
aModel.foo() // delegate to legacyModel
```
