---
title: Mind Map of Backend
date: 2017-07-05 11:09:56
categories: Mind Map
---

Backend tends to have a totally different topics than Web (frontend). What's more, before putting too many items into "Mind Map of Web", creating a new mind-map slot for backend is quite reasonable. Additionally, classical topics about algorithm are also appropriate here for the reason of an obvious different focus from frontend. So all these facts bring about a new mind map for backend.  

# Map

Keywords | Comment & Reference
--- | ---
distributed system | Nginx (read as "engine X") [Concepts of Distributed System][1]
dynamic programming | [Comment](#dynamic-programming), [introduction by comic][2]
compile | [compile introduction][3]
bitwise operation | [is-power-of-2][4], [find-odd-number][5]
greatest common divider | [introduction by comic][6]
max difference within array | [introduction by comic][7]
circular linked list detect | Given node length known [introduction by comic][8]
js dependency | [Comment](#js-dependency) [a js project template][9]
git merge rebase fixup autosquash feature branch | see my post "Feature Branch Workflow", [cheat-sheet][11]
slf4j logback MDC kafka | [Comment](#log)
CAP Eventual Consistency nosql kafka RabbitMQ MongoDB HBase Cassandra Redis Neo4j | [Comment](#cap-nosql)
https | [Introduction][10]
git mergetool kdiff3 | [Comment](#git-mergetool-kdiff3)
flashcard with Anki and Studies | [Anki][12] [Studies][13]
scala | [programming scala](#programming-scala), [scala tip](#scala-tip), [][resources][14], [a blog][17], [reader monad][18]
sbt | [Comment](#sbt-repositories) 
java nio | [Comment](#java-nio)
java concurrency | [Comment](#java-concurrency)
linux command | [commands][15]
disk clean (for Mac) | [link][16]
e-book site (for IT) | [link][20], [link][21]

<!-- more -->

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

# java concurrency 
Question | Answer
--- | ---
Singleton in double-checked locking | 1) synchronized on getInstance is unnecessary for the case of initialized instance and therefore inefficient; 2) a nest synchronized block of the first null check on the singleton class, containing the second null check; 3) add volatile modifier to instance property to prevent other threads from seeing half-initialized instance (also see happen-before guarantee). 
happen-before guarantee | Without it, CPU won't guarantee changed variable is written to memory (so that other threads can see it) in the order of statement in source code. Thread.start and join have this guarantee so that statements happen before are ensured to be available in main memory.  
Singleton in Enum | thread-safety guarantee by JVM and working in case of serialization
CAS | Compare-And-Swap, the fundamental of non-blocking algorithm. Compare the value with the expected one and give up setting to the new value if the compare fails. CPU provides instruction for CAS, resulting better performance over synchronized lock in most cases. Also see Java's atomic types, which are used by many concurrent classes.
aThread.join | The current thread blocks until 'aThread' terminates.
Reentrant Synchronization | A thread can acquire a lock that it already owns.
Java Memory Model | 1) Each thread has its own stack, containing local primitives and object references, while object instances are kept in shared heap. 2) On the other hand, each CPU has its own registers and cache, which keep changed value and sync with the main memory from time to time. 3) Therefore without volatile and synchronized lock, the changed value made by one CPU may be invisible to other CPU.
BlockingQueue & BlockingDeque | It provides operations in 4 ways: throw runtime exceptions; return special value; blocking; return after timeout.
ConcurrentMap | It has multiple table buckets, write operation locks only one bucket and read operation doesn't block.
When volatile is enough | In case only one thread updates the value while other threads are reading the value.
ExecutorService | Executors as a factory can generate multiple ExecutorService like normal thread pool, scheduled thread pool and ForkJoinPool. It can accept(submit) Runnable or Callable tasks and manage lifecycle of these tasks via shutDown and awaitTermination.   
ForkJoinPool(FJP) | Threads in the pool try to execute submitted tasks. execute is fire-and-forget style; invoke is blocking; submit returns ForkJoinTask.
ForkJoinTask | A lightweight form of Future for tasks running within FJP. fork starts execution and join wait for the result. The efficiency comes from the use of pure function and isolated objects as the computation.
CopyOnWrite(COW) | Copy internal data on mutate operation, and no lock on read-only operation. Suitable for the case that mutate operation happens much less than read. What's more, it's better to support batch mode for mutate operation. 
CompletableFuture | Promise in Java available from Java 8.
Lock vs synchronized bloc | lock acquire and release should be put in try-final block, while synchronization has no such an issue.

# java nio
Question | Answer
--- | ---
Types of Channel | File, TCP and UDP; normal(blocking) and asynchronous
Transfer between channels | FileChannel.transferFrom and transerTo are more efficient than read&write since it can rely on file system cache without copy operation.
Transfer between threads with Pipe | Thread A writes data to Pipe.SinkChannel, which then sends the data to Pipe.SourceChannel which can be read by Thread B.
Channel and Buffer | For inbound, data reads from Channel and is put in Buffer; for outbound, Channel gets data from Buffer.
Channel and Selector | For non-blocking channels, multiple channels can register with a selector for events like connect, accept, read and write. Selector.select(it's blocking) returns the number of channels that have events. Selector.selectedKeys is used to iterate these events. 
Capacity, limit and position of Buffer | Invariant: 0 <= mark <= position <= limit <= capacity 
Buffer clear | position <- 0; limit <- capacity. (ready for put operations)
Buffer flip | limit <- position; position <- 0. (ready for get operations)
Buffer rewind | position <- 0. (for re-reading)
Buffer mark and reset | mark keeps a position so that later reset can restore to this position.
Java io vs nio | Stream doesn't provide a built-in buffer, and is blocking but can simplify data processing, while Channel and Buffer need more control over buffered data. Another difference is the possibility of non-blocking operation in nio. Two ways to invoke asynchronous operation: one is Future(though Future.get is blocking), the other is via callback.

# git mergetool kdiff3
[kdiff3](https://sourceforge.net/projects/kdiff3/) is a cross-platform  3-way merge tool. To configure it with git, run:
```
$ git config --global merge.tool kdiff3
$ git config --global mergetool.kdiff3.path <path-to-kdiff3>
// it will generate the following contents in ~/.gitconfig
[merge]
	tool = kdiff3
[mergetool "kdiff3"]
	path = /Applications/kdiff3.app/Contents/MacOS/kdiff3
```

# cap nosql
In the absence of network failure, both consistency and availability can be satisfied. On the other hand, in the presence of network partition, distributed system has to choose between consistency and availability. 

Note that CAP is not saying of choosing two of three. No trade-off when no network issues, though for large scale distributed systems network partition can't be avoided.

No matter which to choose, reconciliation should be made via Eventual Consistency, which serves the fundamental of many NoSQL databases.

HBase: distributed hashmap
MongoDB: distributed json
Redis: distributed hashmap in memory

# log
Simple log facade for Java(slf4j) defines logger interface for log libraries like java logging, log4j and logback. Check its [manual](https://www.slf4j.org/manual.html) for details. Note to introduce dependency in maven, don't directly use slf4j API (but use their adapted packages with log implementation).

Logback is the next generation of log4j. Use `log.debug("foo {}", bar);` to avoid concat string if logger level is higher than DEBUG. Also check Mapped Diagnostic Context [MDC](https://logback.qos.ch/manual/mdc.html) for the support of multi-thread, remote client request (e.g. servlet filter) and microservice.

Another [article](https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying) about log-central system from the author of kafka

# js dependency
Check current dependency with `npm ls --depth=0`

Before introducing a new dependency, check the maturity with `npm view <package-name>`

List outdated dependencies with `npm outdated`

Analyse dependency usage with [depcheck](https://www.npmjs.com/package/depcheck)

Run test on npm package with [Snyk](https://snyk.io/test?utm_source=risingstack_blog)

# dynamic programming
Regarding gold mine puzzle, the key is the generate function of `F(n, w) = max(F(n-1, w), F(n-1, w-p[n-1]) + g[n-1])`.
See implementation at [my git](https://github.com/sevenbamboos/alg-js)

[1]: https://mp.weixin.qq.com/s/5wxIHNMN2MbbSQ6WWA65HQ 
[2]: https://mp.weixin.qq.com/s?__biz=MjM5OTA1MDUyMA==&amp;mid=2655438647&amp;idx=1&amp;sn=4634f712fa4d0236aba60b8e8b7cc2cb&amp;chksm=bd730b408a048256f204695598c0e4f74e75c9582f5b9c740057a69747b306de1a4c308d5388&amp;mpshare=1&amp;scene=1&amp;srcid=0702N84baxNAmMFheg6Ck26Z&amp;key=238113c46368 
[3]: http://fullstack.blog/2017/06/24/%E5%A4%A7%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91%E8%80%85%E9%9C%80%E8%A6%81%E4%BA%86%E8%A7%A3%E7%9A%84%E5%9F%BA%E7%A1%80%E7%BC%96%E8%AF%91%E5%8E%9F%E7%90%86%E5%92%8C%E8%AF%AD%E8%A8%80%E7%9F%A5%E8%AF%86/?nsukey=9G4zvSOVCA0b49Ng4rhqNHkw55dE1OsVGQ3exvS12Nfchm8N38y5eU8Aw2VaHY72/yPT0cyMQK2ewKJjU6F0EqDj3rkpTv/EFl10CW6zk/29dY8DlDLjxh0YRyQFZvmUN4xWwW3e16mpXMDjKu4LVje6cwykadEMWU4klPXFOXWYLBZKqc1ocxBZCnTAH7ZF 
[4]: http://blog.jobbole.com/107689/ 
[5]: http://blog.jobbole.com/106521/ 
[6]: http://blog.jobbole.com/106315/
[7]: http://blog.jobbole.com/108594/ 
[8]: http://blog.jobbole.com/106227/
[9]: https://github.com/wearehive/project-guidelines#readme  
[10]: https://mp.weixin.qq.com/s/StqqafHePlBkWAPQZg3NrA 
[11]: https://sentheon.com/blog/git-cheat-sheet.html 
[12]: https://sspai.com/post/39951 
[13]: https://sspai.com/post/34411
[14]: https://github.com/lauris/awesome-scala 
[15]: https://zhuanlan.zhihu.com/p/28674639
[16]: https://sspai.com/post/40503
[17]: http://danielwestheide.com/scala/neophytes.html 
[18]: http://blog.originate.com/blog/2013/10/21/reader-monad-for-dependency-injection/ 
[19]: http://jonasboner.com/real-world-scala-dependency-injection-di/ 
[20]: http://www.finelybook.com/
[21]: http://www.salttiger.com
