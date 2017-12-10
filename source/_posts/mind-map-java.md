---
title: Mind Map for Java
date: 2017-11-10 15:38:14
categories: Mind Map
---

# Category

Section | Contents
--- | ---
Basic | [Tips](#Basic)
Functional Programming | [Tips](#Functional-Programming)

<!-- more -->

# Functional Programming
Question | Answer
--- | ---
01.groupBy(value -> key) | `Stream::collect(Collectors::groupingBy(value -> key))` See also partitioningBy
02.type alias in Java | Use interface (see code)
03.final variable for closure | Final requirement applies to local variable only (see code)
04.If closure has reference to non-local variable which is not final, how to make function free of side effects | Turn implicit variables to explicit arguments of the function (see code)
05.There are Function and BiFunction. How about TriFunction | Using curring is another way to avoid TriFunction (see code
06.Partially bind the first/second parameter | Deduct from types to create curried function (see code)
07.The size of a thread | 1064 KB for 64-bit JVM (size of stack)
08.Give meaningful name for built-in functional interface | Define new functional interfaces (with same function signature as built-in functional interfaces) and use the new interfaces in class/method definition. Then use anonymous lambda in the application.
09.Pattern matching in Java | Define a Case class extended from `Tuple<Supplier<Boolean>, Supplier<Result<T>>>`. See com.samwang.common.EmailValidation (alg-java in my git)
10.Handle effects in a loop | One way is to fold effects into one and execute them afterwards (in a batch mode or delay the execution until necessarily).
11.Why classes shouldn't have several properties of the same type (in modeling) | No compiling error when using an incorrect property. Solution is to use value types like Money, Weight, Age (rather than double, int).
12.Make recursion function stack-safe | 1) Turn to tail-recursion 2) Use heap-based TailCall, which has two sub-classes: Return and Suspend. See com.samwang.common.TailCall (alg-java in my git)
13.Turn to tail-recursion | Define a helper function with an accumulator.
14.Another way to look at Fibonacci series 0,1,1,2,3,5,8,13 | A series of pairs (tuples): (0,1),(1,1),(1,2),(2,3),(3,5),(5,8),(8,13) And the generation method is `x -> new Tuple<>(x._2, x._1 + x._2)`
15.Function memorization | Wrap function in com.samwang.common.Memoizer (alg-java in my git). For functions with more than one parameter, one way is to use Tuple2, Tuple3 (and use these tuples as the key in Memorizer). Another (better) way is to memorize the curried function (see code). In case there are huge different results to be kept in cache, use soft or weak references (and WeakHashMap) in Memorizer.
16.java.util.Objects | Helper functions for comparison, equal, hash-code, null-check, etc.
17.Functional list in Java | Define abstract class List with two sub-classes Nil and Cons. See com.samwang.common.List (alg-java in my git).
18.What's good for StringBuilder::append to return this | In case StringBuilder is used as an accumulator, it saves one line of code to explicitly return the builder (see code).
19.Implement foldRight with foldLeft for the purpose of stack safety | `list.reverse().foldLeft(identity, x -> y -> f.apply(y).apply(x));`
20.Given concat(head, tail), what's good to implement List::flatMap with foldRight instead of foldLeft | foldLeft: (((1,2),3),4); foldRight: (1,(2,(3,4))). So foldRight works well with `concat(f.apply(element), list_for_accumulation)`
21.Implement variance (with mean) | (see code)
22.
```
// 02
interface BinaryOperator extends Function<Integer, Function<Integer,Integer>> {}
BinaryOperator add = x -> y -> x + y;

// 03
void aMethod() {
  int foo = 1; // final can be omitted as long as no change afterwards
  Predicate<Integer> greatThanFoo = x -> x > foo;
  foo = 2; // compile error
}

int bar = 1; // bar is not a local variable so it has its own lifecycle with method call 
void bMethod() {
  Predicate<Integer> greatThanBar = x -> x > bar;
  bar = 2; // no error but bMethod gives different results which is no good 
}

// 04
Predicate<Tuple<Integer,Integer>> greatThanBar = tpl -> tpl._1 > tpl._2;
greatThanBar.test(new Tuple<>(x, bar))
// That's the reason to introduce BiPredicate to avoid ugly Tuple syntax

// 05
<A,B,C> Function<Function<A,B>, Function<Function<B,C>,Function<A,C>>> compose() {
  return a2b -> b2c -> a -> b2c.apply(a2b.apply(a));
}

// 06
public static <A,B,C> Function<B,C> partialA(A a, Function<A,Function<B,C>> f) {
  return f.apply(a);
}
public static <A,B,C> Function<A,C> partialB(B b, Function<A,Function<B,C>> f) {
  return a -> f.apply(a).apply(b);
}

// 15
Function<Integer, Function<Integer, Integer>> addWithCurry = x -> y -> x + y;
Function<Integer, Function<Integer, Integer>> addWithCurryWithMemo = memorize(x -> memorize(y -> x + y));

// 18
TailCall<StringBuilder> toString(StringBuilder acc, List<A> list) {
  return list.isEmpty()
    ? ret(acc)
    : sus(() -> toString(acc.append(list.head()).append(","), list.tail()));
}

// 21
val sum = list -> list.foldLeft(0, a->b->a+b);
val mean = list -> list.isEmpty ? Optional.empty() : Optional.of(sum.apply(list) / list.length);
val variance = list -> mean.apply(list).flatMap(m -> mean.apply(list.map(x -> Math.pow(x-m, 2))); 

```

# Basic
Question | Answer
--- | ---
01.Classloader | Three classloaders: Bootstrap(rt.jar), extension and application(classpath).
02.OOPS principles | Abstraction with encapsulation, (type system supports) inheritance and thus polymorphism.
03.Object oriented VS object based | ES5 as object based language, provides abstraction and encapsulation, but no native support of inheritance and polymorphism
04.What happen if multiple interfaces have the same default method and a class implements these interfaces | Compile error.
05.Aggregation VS composition | Objects get destroyed along with the composite object, while aggregated objects exist.
06.super() and this() | They (not both of them) must be the first statement for a constructor.
07.Execute a program with main method | No way to do it since Java 7.
08.Override for static(class) method | No way as inheritance doesn't apply to class.
09.Overloading VS overriding | Overloading happens at compile time, while overriding at runtime.
10.Covariant return type | It's allowed to return a sub-type in method of sub-class. (see code)
11.Marker interface VS annotation | Interface provides static check during compiling, while annotation has to be checked at runtime. (see code)
12.Pass-by-value or pass-by-reference | Java is always pass-by-value. (see code)
13."foo" VS new String("foo") | The former is in string constant pool, while the latter is a normal string object. (see [this](http://www.thejavageek.com/2013/06/19/the-string-constant-pool/)) 
14.Comparator VS comparable | The former is a util interface comparing two objects, while the latter compares another object with itself, which means it is implemented by compared types while comparator is used by sorting functions.
15.Why Object::hashCode() is native | Performance concern because it looks for an integer representation of an object reference on the heap. 
16.What's the benefit of Collections::copy() over collection constructor or addAll | It doesn't involve reallocation but instead trigger out-of-index error if happened.
17.Return value for high-order function | For functional interface, return a lambda (instead of an anonymous class).
18.java.time package since Java 8 | LocalDate, Year, MonthDay, DayOfWeek. Also refer to TemporalAccessor for use suggestion (see code)
19.@SafeVarargs | For methods with variable number of arguments, JVM uses array under the hood. When the type of arguments is type parameter, JVM uses Object[] which could lead ClassCastException. That's the reason JVM gives warning during compiling. The annotation is used to by-pass the warning.

Continue at P250

```
//10
class Animal {
    public Animal giveBirth() { return this; }
}
class Cat extends Animal {
    @Override public Cat giveBirth() { return this; }
}

//11
@SomeAnnotation
class Foo { ... }

  if (!obj.getClass().isAnnotationPresent(SomeAnnotation.class)) {        
    // do something       
  }   

//12
Dog a = new Dog("A");
void foo(Dog dog) {
  dog = new Dog("foo");
}
foo(a);
//a's name is A, not foo. a is a reference in stack (instead of heap), that points to a Dog instance.
//Inside method foo, dog is a different reference that points to the same Dog instance.
//So after foo, a's name is still A and the reference of dog has been discarded after foo.
//That's the reason of pass-by-value. In this case, it's pass-a-reference-by-value.

//18
ChronoUnit.DAYS.between(d1,d2)
d1.with(TemporalAdjuster.firstDayOfMonth());
d2.with(TemporalAdjuster.next(DayOfWeek.MONDAY));
```
