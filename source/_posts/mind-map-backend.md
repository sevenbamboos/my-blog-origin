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
Algebraic Data Types | [Comment](#adt), [blog 1,2,3][22]
java nio | [Comment](#java-nio)
java concurrency | [Comment](#java-concurrency)
linux command | [commands][15]
disk clean (for Mac) | [link][16]
e-book site (for IT) | [link][20], [link][21]

<!-- more -->

# adt
Code | Algebra
--- | ---
Void | 0
(), Unit | 1
a \| b | a + b
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
[22]: http://chris-taylor.github.io/blog/2013/02/11/the-algebra-of-algebraic-data-types-part-ii/ 
