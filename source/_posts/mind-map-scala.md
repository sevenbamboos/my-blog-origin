---
title: Mind Map of Scala 
date: 2017-09-05 21:09:56
categories: Mind Map
---

# Map

Keywords | Comment & Reference
--- | ---
Concurrent programming in Scala | [Q&A](#concurrency-scala)
Programming Scala | [Q&A](#programming-scala)  
Scala tip | [Q&A](#scala-tip)
Algebraic Data Types | [Comment](#adt), [blog 1,2,3][4]
sbt | [Comment](#sbt-repositories) 
reader monad | [reader monad][3] 
resource | [resource][1]
an introduction blog | [blog][2]

<!-- more -->

# concurrency scala
Question | Answer
--- | ---
1. What's AnyRef::synchronized | A method defined on AnyRef(=Object in Java); Apart from the single-thread executing of the block, it also ensures all writes to memory in the block are visible to other threads later when they execute the block.
2. How to prevent deadlock due to ask for multiple resources | Give an order for resources so that they can be acquired in a fixed order. (see code 01)
3. What's a daemon thread | JVM main process will terminate when all threads are daemon.
4. How to refer to a by-name parameter without executing | `def foo(block: => Unit) = { val aBlock = () => block }` In fact, by-name parameter looks like `def foo(block: ()=>Unit) = { block() }`. The difference is parameter needs () to invoke, while not necessary for by-name. Another difference is by-name param should be invoked with `{}` block, which is not type of `()=>Unit`
5. How to avoid busy-wait via wait/notify | Wrap wait/notify in synchronized block and use while loop with wait (see code 02)
6. What's graceful shutdown and when not to use it | The thread provides a shutdown flag and the run method stops when it sees the flag is set. (see code 03)
7. How to shut down ExecutorService | shutdown() followed by awaitTermination() to wait for completion of submitted tasks.
8. What is lock-free operation | Given a set of threads, at least one thread always completes after a finite number of steps, regardless of the progress of other threads. CAS-based way is necessary but not sufficient (hard to prove), but synchronized statement is lock-based.
9. How to apply CAS to state check | AtomicReference[State], which has get and compareAndSet methods. Note, it ALWAYS uses reference equality and never call equals method.
10. How to assign default values to a variable (although Option is a better choice) | `val foo: AnyRef = _` 0,false or null depends on variable type.
11. What should be avoid in lazy initialization block (or singleton object constructor) | Blocking operation which could bring about deadlock
12. How to choose a object for synchronization | Prefer a dedicated private dummy object to this or public fields to reduce the risk of deadlock
13. When to choose bounded concurrent queue (e.g. producer-consumer problem) | It's for faster producer to control the queue size. For faster consumer, unbounded queue is better (less memory). 
14. How to convert collection between Java and Scala | `scala.collection.convert`
15. Is every method in concurrent map atomic | No. Double-check doc. For example, putIfAbsent is, while getOrElseUpdate is not.
16. How to find a change to a group of things that could be modified by multi-threads | Assign each item a timestamp and update it on each modification. Then for another thread just to sum all timestamp twice (before and after some steps) and compare the results.
17. How to eliminate while loop with recursion | Put a loop counter as one function parameter.
18. How to get support of (Unix) shells | `scala.sys.process`
19. When will callback be invoked by Future | It's NOT always immediately after completion. If Future already completes and then we install a callback, then the callback is invoked at that moment.
20. How to install callback to Future | foreach(T=>U), onComplete(Try[T]=>U) and failed.foreach. Note onComplete can take a block to do pattern match like `{ case Success... case Failure... }` (see code 04)
21. How to do exception handling for Future | `f.failed foreach{ case NonFatal(e) =>... }` Don't catch fatal errors. 
22. What's the relation between Promise and Future | Promise keeps a one-time assignment to a variable either with a value or exception, which makes it suitable to construct Future (see my blog `Promise and Future`)
23. How to block (main) thread to wait for asynchronous computation | scala.concurrent.Await::ready and result.
24. How to indicate execution context there is a blocking part inside a Future | blocking { ... }
25. Why does performance test need warmup | For language with just-in-time compilation like Java, bytecode as the output of compiler will be run first as interpreted mode until JVM decides to turn part of frequent visited into machine code and thereby that part will run in the steady state, which is much faster than before. That's the reason we need warmup (maybe hundreds of times of test code) before doing performance measurement. 
26. In Rx, what is good to define Subject to implement both Observable and Observer | Observer may not be available when creating Observable. So Subject can be a mediator between Observable and final observers.
27. What is Software Transactional Memory(STM) | Wrap atomic block around shared resources (like what atomic reference does but STM provides an easy way to manage multiple references) and automatically retry the block if any resource has been changed by other threads (like what database transactions do). Resource needs to be wrapped in STM reference (to be known by STM) before atomic block. Inside block, resources need to accessed by the references. And the references should never be used outside the block (not even in afterCommit/afterRollback methods, which perform operations without the danger of executing them multiple times when retrying). Also note to avoid long-running like infinite loop in the block.
28. In STM, what happens when there is nested atomic block | In ScalaSTM, wrapped block will become the top-level block and rollback retry start from the topmost block.
29. How to express a method whose return type is the class itself | `def foo(...): this.type`

```
// 01
def transfer(from: Account, to: Account, amount: Money) = {
	def doTransfer() = {
		from.balance -= amount;
		to.balance += amount;
	}
	if (from.uid < to.uid)
		from.synchronized { to.synchronized { doTransfer() } }
	else
		to.synchronized { from.synchronized { doTransfer() } }
}

// 02
val lock = new AnyRef
val worker = new Thread {
	lock.synchronized {
		while (...) lock.wait()
		...
	}
}

lock.synchronized {
	...
	lock.notify()
}

// 03
object Worker extends Thread {
	var terminated = false
	def shutdown() = resource.synchronized {
		terminated = true
		resource.notify()
	}
	def run() = resource.synchronized {
		while (resource.isNotAvailable && !terminated) resource.wait()
		if (!terminated) {
			doSomething(resource)
			run()
		}
	}
}

// 04
foo.onComplete {
	case Success(i) => ...
	case Failure(err) => ...
}
// identical
foo.onComplete { t => t match {
	case Success(i) => ...
	case Failure(err) => ...
}}
```

# adt
Code | Algebra
--- | ---
Void | 0
(), Unit | 1
a \ b | a + b
(a,b) | a * b
a -> b | b ^ a

Examples:
Linked list: Nil | Cons a (List a)
Algebra: L(a) = 1 + a * L(a) // Taylor Series
wolframalpha.com: search with `Series[1/[1-x]]` => 1 + a + a^2 + a^3 ... 
Meaning: a list is either empty or containing a single a, or a list of two a, or three a...

Binary tree: Nil | Node a (Tree a) (Tree a)
Algebra: T(a) = 1 + a * (T(a)^2)
wolframalpha.com: search with `Series[[1-Sqrt[1-4x]]/[2x]]` => 1 + a + 2 * a^2 + 5 * a^3 ... 
Meaning: a binary tree is either empty or containing a value of type a, or two values of type a in two ways, or three values of type a in five different ways...

# sbt repositories
~/.sbt/repositories
Please note that maven-central is fallback in case other repositories have invalid-hash-value error. aliyun is for fast-access in China.
```
[repositories]
	local
	aliyun-ivy: http://maven.aliyun.com/nexus/content/groups/public, [organization]/[module]/(scala_[scalaVersion]/)(sbt_[sbtVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext]  
	aliyun-maven: http://maven.aliyun.com/nexus/content/groups/public 
	typesafe: http://repo.typesafe.com/typesafe/ivy-releases/, [organization]/[module]/(scala_[scalaVersion]/)(sbt_[sbtVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext], bootOnly
	sbt-plugins-repo: http://repo.scala-sbt.org/scalasbt/sbt-plugin-releases/, [organization]/[module]/(scala_[scalaVersion]/)(sbt_[sbtVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext]
	play: http://private-repo.typesafe.com/typesafe/maven-releases/
	sonatype-snapshots: https://oss.sonatype.org/content/repositories/snapshots
	typesafe-releases: https://repo.typesafe.com/typesafe/releases
	typesafe-maven-releases: http://repo.typesafe.com/typesafe/maven-releases/
	typesafe-ivy-releasez: https://repo.typesafe.com/typesafe/ivy-releases, [organization]/[module]/(scala_[scalaVersion]/)(sbt_[sbtVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext]
	maven-central
```

# programming scala
TODO
How to join other worker threads in the main thread?
Implement Automatic Resource Manage (ARM) in Java

Question | Answer
--- | ---
Where to find API and source code | scala-lang.org has API documentation and source code link (to Github) on most pages
How to REPL with SBT | sbt -> console will load the project in classpath. Ctrl-D or :quit to quit REPL mode from SBT
How to check definitions in a class | javap (no way to see the implementation without source code?)
How to define detailed messages (for Actor) | Put signals in an object like: object Messages { case object ...; case class ...; } (see source code 01)
How to put multiple lines of code in one pattern match branch | No need to wrap them in {...} because => and the next `case` defines the boundary
How to define an `init` message (for Actor) that won't be used in anywhere else | In the file of Actor, `private object Init` 
When will `Future` start to execute | It starts after `Future.apply()`. No need to worry about when to get the result because it's always there inside the future instance (see source code 02)
When explicit type annotation is needed | Scala does local type inference, so method parameters, return type of recursive and overloaded methods (which is disallowed for nested function) need type annotation. 
Given a List[Int], how to call `def foo(is: Int*)` | foo(lst :_*) `:_*` is read as turning whatever (List?) to multiple variables
How to use Java type whose name is a reverse word in Scala | back quotes e.g. java.util.Scanner.\`match\`
How to format multi-line String | use """ and stripMargin (see source code 03)
How to exclude a few types when doing import | `import xxxpackage.{ Foo => _, Bar => Foo }`
How to group class, object and function so that they are available with one import | package object (see source code 04)
When can't function be called with parentheses | For function having no parameters, if it's defined without parentheses, caller must invoke it WITHOUT parentheses. Otherwise, empty parentheses can be omitted. So a function without side-effects can be defined without parentheses.
When can functions be called in a chain without => like `List(...) filter isEven foreach println` | Functions take a single argument
Overhead of duck typing | It needs reflection to find the method(property), and needs to import language.reflectiveCalls. On the other hand, it can support legacy (external) resource that is out of control of conforming to a trait (see source code 05)
How to define a do-while block | 1) Use by-name (lazy) parameter for both guard and body to make sure they only get evaluated when being used; 2) If use recursive to achieve loop effect, annotate with tailrec; 3) Separate guard and body into two parameter lists so that it looks like a native loop (see source code 06)
When to go for `scala.Enumeration` | It's lightweight compared to `case class`. Note it's an object, and needs `type foo=Value` to give it a type. Each value needs to be added via `Value` method (see source code 07)
Is it possible to extend traits during creating objects | Yes. No need to define a sub-type with the traits in advance
How to match a case with a variable defined outside match block | case \`variableOutside\` => ...
Whether do parentheses need for `if` guard in a case for pattern match | No. `if` and `=>` provide the boundary.
:: vs +: vs :+ | +: is for Seq, which is super type of List. :: is for List. +: is prepend while :+ is append. For List, :+ takes O(n) time to trave to the last element. For Vector which is IndexedSeq, :+ takes O(1)
How to match elements from the end | `case rest :+ end => ...` Under the hood, :+ is an infix extractor, acted like :+(rest, end).
How to deconstruct multiple elements (for sequence) | `case first +: second +: tail => ...` or `case Seq(first, second, _*) => ...`
Is there any performance penalty for lazy values | Yes. There is a guard for each lazy value to check whether it has been initialized. So after the first time of initialization, the check becomes unnecessary for later use.
How to bind variable to multiple parameters in pattern match | `case Foo(props @ _*) => ...`
How to match Seq[T] for different T | Since generic T will be erased at runtime, workaround is to get head and do a nested match (see source code 08)
How to define an abstract method (no side effects) in super type | `def aMethod: ReturnType` then sub-type has the freedom to use `val aMethod: ReturnType` as implementation, or even `case class SubType(aMethod: ReturnType)` Note there is no parentheses for aMethod in super type.
Can PartialFunction be passed into map | Yes. `trait PartialFunction[A,B] extends (A) ⇒ B` Note to add default case otherwise there would be match error (at runtime)
What does Predef.implicitly do | Take a type and make an implicit value, so that to save an implicit parameter. `def implicitly[T](implicit e: T) = e`
How to class cast (and test cast) | Any::asInstanceOf[T] and Any::isInstanceOf[T]. Note generic type will be erased at runtime so better not involve it
What is implicit classes | the class' primary constructor is available for implicit conversions when the class is in scope
What's the difference between with or without val(var) in class constructor | Without val(var), variable is visible in class block but not as a property. case class can omit val(var) and still keep them as properties
What is <:< | Provide evidence that for A <:< B (or written as <:<[A,B]), B is super type of A. (see TraversableOnce::toMap, evidence is implicit so no extra work but can enforce the type check by compiler) 
How to overload functions just with different parameter types | Create implicit objects for different types and add an implicit argument list with type object at the end of function (see source code 09).
What is type class pattern and when to use it | ad hoc polymorphism, that is add capability to existing classes (see source code 10). Use it when only a few of classes need a particular behavior. If a function takes a list of objects in different types, only one implicit class would be passed in. If we therefore implement the behavior for multiple types in one implicit class, it leads to an ugly solution. 
What is scala.util.control.TailCalls | Make recursive calls on the heap instead of the stack. (see source code in ScalaDoc for fib with tailcall)
How to invoke function of argument list with a tuple | Function.tupled[a1,a2,b](f: (a1,a2)=>b): ((a1,a2)) => b // there are more tupled for more parameter (as well as untupled)
How to turn PartialFunction to a full function | PartialFunction::lift() // MatchError will become Option
How to overwrite type defined in Predef | Use a type alias and a val as a companion object for its apply (see source code 11)
How to implement fib with Stream | BigInt(0) #:: BigInt(2) #:: fibs.zip(fibs.tail).map { n => n._1 + n._2 } 
What's the point to have both reduceRight and reduceLeft | reduceLeft supports tail recursive, while reduceRight can handle infinite lazy stream. reduceLeft => rl(f(head) +: accum, tail) => ((1+2)+3)+4; reduceRight => f(head) +: rR(tail)(f) => 1+(2+(3+4)) 
What is persistent data structure | It can keep the previous state after being modified. For binary search tree (BST), each change to one node will also change at most log(n) nodes (along the path from the changed node to the topmost)
How many times of iteration for chained map and filter | Intermediate operations will be combined and evaluated in one iteration until a terminal operation like reduce. 
What is universal trait | Derive from `Any`, including methods only and the implementation has no initialization of its own. If the trait is used as type parameter(generic) or function signature, the wrapper will be allocated (in heap). (see source code 12) 
What is value class | Has to be top-level type, only one val argument, no secondary constructors, only methods, can't override equals and hashCode, inherit from universal trait. As a wrapper, it's not necessary to allocate itself in heap. (see source code 12)
How to define unary method | def unary_X : ... // X is the unary operation and put a space between operation and :
When to use inheritance | Concrete class should never be subclassed unless for mixing orthogonal behaviors or unit testing; never split logical state (e.g. related to hashcode or equality) across parent-child boundary.
How to mix traits when creating object | `val foo = new Foo() with BarTrait` // not extends here
When mixing multiple traits, what's the order of type hierarchy | For each type from RIGHT to left, find hierarchy list and append it to the final hierarchy. Then scan from the end of the final list and remove duplicated ones before it.
When mixing multiple traits, what's the order of initialization | For each type from LEFT to right, execute the type body.
Class or trait | Mixins are for adjunct behavior. If a trait is used most as a parent of classes, so that child classes behave as the trait, try to turn the trait to a class. Refer to Liskov substitution principle: replace a parent class with a child should not make change to client code.
Is there variance annotation for paremeterized method | No, it's applied to types only, meaning for methods it's invariant.
What's the variance of mutable types | Invariance. It can have both getter and setter, which means it appears at method parameter as contravariant and at method return type as covariant. So it must be invariance.
How to parse arguments of command line with pattern match | `case ("-p" :: param :: tail) => doParse(tail, result.copy(p=param))` (see source code 13)
When value types (e.g. Int, Boolean) need to be allocated on the heap | They're represented as Java primitives so stay in stack by default. But Rich* types provide more methods for them and if one of these methods are used, it needs to allocate an instance on the heap.
When to override method | Template pattern is a way to walkaround override and defines fine-tuned abstract methods in super type; if super type provides a simple (mock) implementation for a method, then override is acceptable; another case is to provide separate concerns on top of the basic implementation.
What is `def foo[T <: Bar](...` | T must be sub-type of Bar (In Java, <? extends Bar>)
What is `def foo[T :> Bar](...` | T must be super-type of Bar (In Java, <? super Bar>)
What is Context Bounds `def foo[T : Bar](...` | There must exist Bar[T]. (see source code 14) 
When to use parameterized types and when to abstract types | In case of constructor needs type parameter in arguments, the former is the only approach.
What is structural types (ducking type) | For example in observer pattern, `type Observer = {def notify(state: Any): Unit}`. To decouple the method name, it can be simplified as `type Observer = State => Unit`. But it has a limit to can only handle the type with one method.
What is existential types | Type parameters are erased in JVM byte code. So two functions with same name and same arguments but different type parameters can't co-exist (overloaded). In case of Seq, workaround is observe each item and apply function to it (see source code 15) Also note existential types can have type bounds like `Seq[_ >: Bar <: Foo]`

```
// source code 01
object Messages {
  object Exit
  object Start
  object Stop
  case class Inbound(message: String)
}

// source code 02
val aFuture: Future[Int] = Future {
	// do the job
}
// aFuture starts to run in another thread
aFuture foreach { res =>
	// res is available here
}

// source code 03
// | is the default leading char
val s =
	s"""First line
	   |Second line
	""".stripMargin

// source code 04
package samw.hl7
package object api {
	class {...}
	def ...
}

// source code 05
import scala.language.reflectiveCalls
def clearAll(ts: {def clear(): Unit}*) = {
	ts.foreach(_.clear())
}
def clear[T <: Clearable] (ts: T*) = {
	ts.foreach(_.clear())
}

// source code 06
@annotation.tailrec
def doWhile(guard: => Boolean)(body: => Unit): Unit = {
	if (guard) {
		body
		doWhile(guard)(body)
	}
}

doWhile(i > 0) {
	//...
}

// source code 07
object Animal extends Enumeration {
	type Animal = Value // 1) Value
	val Dog, Cat, Pig = Value // 2) Value
}

// source code 08
case Nil => ... // necessary to cover all cases
case head +: _ => head match {
	case type1: Type1 => ...
	case type2: Type2 => ...
	case _ => ...
}

// source code 09
implicit object IntMaker
implicit object StringMaker
def m(seq: Seq[Int])(implicit i: IntMaker.type): Unit = println(s"seq[int]: $seq")
def m(seq: Seq[String])(implicit i: StringMaker.type): Unit = println(s"seq[string]: $seq")

// source code 10
class Foo ...
class Bar ...
implicit class Foo2Json(foo: Foo) {
	def toJson ...
}
implicit class Bar2Json(bar: Bar) {
	def toJson ...
}
println(new Foo(...).toJson)
println(new Bar(...).toJson)

// source code 11
package util
package object datastructs {
  type Seq[+A] = scala.collection.immutable.Seq[A]
  val Seq = scala.collection.immutable.Seq
}
// import util.datastructs._

// source code 12
trait Formatter extends Any { // universal trait
	def format(format: String): String = ???
}
trait AnotherTrait extends Any {...}

// Through String is not from AnyVal (AnyRef instead), it's still a value class
class PhoneNumber(val s: String) extends AnyVal
	with Formatter with AnotherTrait {
	override def toString = {...}
}

// source code 13
case class Args(p1: String, p2: String)

// exit returns Nothing, which is subtype for everything
// so any function can call exit without breaking type checking
def quit(msg: String, status: Int): Nothing = {
	if (msg.length > 0) println(msg)
	sys.exit(status)
}

def parseCommandLine(args: Array[String]): Args = {
	def doParse(argList: List[String], result: Args): Args = argList match {
		case Nil => result
		case ("-h" | "--help") :: Nil => quit("", 0)
		case ("-p1") :: p1 :: tail => doParse(tail, result.copy(p1 = p1))
		case ("-p2") :: p2 :: tail => doParse(tail, result.copy(p2 = p2))
		case _ => quit(s"Unknown argument ${argList.head}", 1)
	}
	return doParse(args.toList, Args("", ""))
}

// source code 14
import math.Ordering
case class MyList[A](list: List[A]) {
	def sortBy1[B](f: A => B)(implicit order: Ordering[B]): List[A] =
		list.sortBy(f)(order)

	def sortBy2[B: Ordering](f: A => B): List[A] =
		list.sortBy(f)(implicitly(Ordering[B]))
}

def main(args: Array[String]): Unit = {
	val lst = MyList(List(4,3,1,5,2))
	println(lst.sortBy1(i => -i))
	println(lst.sortBy2(i => -i))
}

// source code 15
def double(seq: Seq[_]): Seq[Int] = seq match {
	case Nil => Nil
	case head +: tail => toInt(head)*2 +: double(tail)
}

private def toInt(x: Any): Int = x match {
	case i: Int => i
	case s: String => s.toInt
	case x => throw new IllegalArgumentException(s"Can't turn $x to Int")
}
```

# scala tip
Question | Answer
--- | ---
compose, andThen | f _ compose g _ //=> f(g()); f _ andThen g _ //=> g(f())
curried and Dependency Injection(DI) | minusCu = minus.curried //Int=>Int=>Int; minusCu(5)(2) //=3; Curried function can be used for DI, especially with implicit parameter. To avoid to give implicit dependency to each function, Reader monad comes to help. 
Explicitly Typed Self References | this: Foo with Bar => // this trait/class/object must extend from Foo and Bar, so that this can use code in Foo and Bar (without explicit dependency of type if this is abstract class or trait). In Cake Pattern (great way for DI), it's used in service that declares dependency to other services.
Structural typing (AKA Duck typing) | def greet(duck: {def quack(s: String): String}) = duck.quack(s"Hello ${duck.quack("Joe")}") // not only method, also include val. Multiple items can be separated with ; or new line. Also a great way for DI (see [link][19] for other ways of DI in scala).
Partially applied function | minusBy2 = minus(_:Int,2); minusFunc = minus _ 
PartialFunction | val int2bool: PartialFunction[Int, Boolean] = {case 0 => false; case 1 => true}; int2bool.isDefinedAt(2) //=> false 
isInstanceOf | scala.Any::isInstanceOf[T] vs Java's version: static <T> boolean isInstanceOf(Class<T> t, Object obj) {return t.isAssignableFrom(obj.getClass());}
apply in class and object | In class, it serves as method, while in object it's a smart constructor. For example in List, apply(index: Int):T and apply(xs: T*): List[T]
Try::toOption | def get(i: Int): Option[T] = Try(lst(i)).toOption // in case i is an illegal index, it's handy to avoid length check. Internally, apply accepts by-name parameter (treated as () -> T, lazy evaluated), and wraps it with try-catch.
Map(1->"a",2->"b") and get | -> is defined in Predef to return Tuple2. Map's apply accepts multiple Tuple2. Note Map::apply(key) returns Value while Map::get(key) returns Option, which can avoid NoSuchElementException 
Pattern match vs for comprehension | Suppose fn and ln are both Option, for (f <- fn; l <- ln) yield (f,l) //return Option[Tuple2]; (fn, ln) match {case (Some(f),Some(l)) => ...; case _ => None} // more elegant

[1]: https://github.com/lauris/awesome-scala 
[2]: http://danielwestheide.com/scala/neophytes.html 
[3]: http://blog.originate.com/blog/2013/10/21/reader-monad-for-dependency-injection/ 
[4]: http://chris-taylor.github.io/blog/2013/02/11/the-algebra-of-algebraic-data-types-part-ii/ 
