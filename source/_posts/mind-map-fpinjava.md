---
title: Functional Programming in Java
date: 2017-12-16 12:38:14
categories: Programming
---

Based on Functional Programming in Java, a book similar to the red bible of Scala and explains reasons why Scala introduced features like monad Option, List, Stream, Try and comprehension.

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
22.Optional::orElseGet vs orElse | The former supports laziness which is critical for function evaluation; the latter is suitable for literal (because literal has been evaluated already by compiler).
23.Exception handling with Result | See com.samwang.common.Result (alg-java in my git), which is more powerful than Option in that it has 3 subtypes to represent 3 cases:Success, Failure and Empty.
24.Give meaningful (error) message for Result.Empty or Result.Failure | abstract Result<T> mapFailure(String message)
25.Find the last one using foldLeft | (see code)
26.Loop without loop | Accumulator + recurrence
27.Return an item at an index with foldLeft | fold with a Tuple as identity in the format of (failure + index). See Result::getAt_ (alg-java in my git).
28.Reason to use joinfork thread pool to do list parallelization. | Hard to predict computation time so having to divide the list into a large number of sublists. 
29.Algorithm to show the ugliness of imperative style | Find the first ten primes (see code)
30.Remove item from Binary Search Tree (BST) | After removing the item, the left and right node need to be merged. See Tree::remove and Tree::removeMerge (alg-java in my git)
31.General merge for BST | See Tree::merge (alg-java in my git)
32.Day-Stout-Warren algorithm for balanced tree | 1, Rotate left until both branches are nearly equal. 2, Apply rotation to the right branch. 3, Apply right rotation to the left branch. 4, Stop when the height = log2(size)

<!-- more -->

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

// 25
Function<Result<A>, Function<A, Result<A>>> f = x -> y -> Result.success(y);
Result<A> lastOption() { return foldLeft(Result.empty(), f); }

// 29
// Algorithm description
// 1. Take a list of integers (positive)
// 2. Filter the primes
// 3. Return a list of the first ten results

// Algorithm translated in imperative style
// 1.  Take the first integer
// 2.  Check whether it's a prime
// 2.a If so, store it in a list
// 3.  Check whether the resulting list has ten elements
// 3.a If so, return it as the result
// 3.b If not, increment the integer by 1 (or 2 to skip even, how to avoid multiples of 3,5,...)
// 4.  Go to Step 2 

```
