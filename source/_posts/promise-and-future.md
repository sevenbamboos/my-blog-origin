---
title: Promise and Future
date: 2017-09-17 11:09:56
categories: Programming 
tags:
- Scala
---

# What is Future
Future is a place to hold a value (that may not be available yet). Future has two parts: asynchronized computation (a block provided by application) and the computation result.

## Future's computation
In language such as Scala, a block of computation is actually by-name parameter like this:
```
val fetchPatientDetails = Future{
  val session = getSession(credential)
  session.getPatientInfo(id)
} // it's Future<PatientInfo>
```

<!-- more -->

## Future's result
To get the result wrapped in Future, one way is to invoke a blocking method (getValue, just as what Java's Future does). It's very inconvenient and just letting application to take responsibility of polling. After all, the result may not be available or suddenly become accessible any moment after Future start, which could make the whole application undeterministic. Another way is to install callbacks to Future so that on completion it will invoke the proper callback depending on whether successful or failed. Note: callback is invoked by Future in the meaning of `inversion of control`
```
// install a callback for happy path
fetchPatientDetails foreach { patient =>
  val address = getMajorCommunicationChannel(patient.id)
  UI.confirmPostalAddress(address)
}

// install callbacks for both success and failure
fetchPatientDetails onComplete {
  case Success(info) => ...
  case Failure(e) if NonFatal(e) => ... // try not to catch fatal errors
}
```

# What is good to have Future combinator
It could be tedious to make a sequential of Future with nested callback like this:
```
fetchPatientDetails foreach { patient =>
  val fetchPatientReports = Future{
    session.findReportsByPatient(patient.id)
  }

  fetchLatestReport foreach { reports =>
    val latestReport = reports sortWith(_.createDate > _.createDate).take(1)
    val fetchComparisonStudies = Future{
      session.findComparisonStudies(latestReport.id)
    }

    fetchComparisonStudies foreach { studies =>
      UI.displayReport(latestReport, studies)
    }
  }
}
```

To simplify the code structure, combinator is a solution.
```
val fetchLatestReport = fetchPatientDetails map { patient =>
  val reports = session.findReportsByPatient(patient.id)
  reports sortWith(_.createDate > _.createDate).take(1)
}

val fetchComparisonStudies = fetchLatestReport map { report =>
  val studies = session.findComparisonStudies(report.id)
  (report, studies)
}

fetchComparisonStudies foreach { (report, studies) =>
  UI.displayReport(report, studies)
}
```

Actually in Scala, combinator code can be simplified further with for-comprehension:
```
val session = getSession(credential)
val (report, studies) = for {
  patient <- Future { session.getPatientInfo(id) }
  reports <- Future { session.findReportsByPatient(patient.id) }
  latestReport = reports sortWith(_.createDate > _.createDate).take(1)
  studies <- Future { session.findComparisonStudies(latestReport.id) }
} yield (latestReport, studies)
UI.displayReport(report, studies)
```

A huge improvement compared to the version with callback. Admittedly, Java's Future is even worse in that it has no way to install callback other than calling the blocking method get.

# What is Promise (how to build Future)
> While futures are defined as a type of read-only placeholder object created for a result which doesn’t yet exist, a promise can be thought of as a writable, single-assignment container, which completes a future. That is, a promise can be used to successfully complete a future with a value (by “completing” the promise) using the success method. 

```
val p = Promise[Seq[Task]]()
val fetchOpenTasks = p.future

val prepareForNotification = Future {
  val tasks = session.findOpenTasksForPatient(id)
  if (tasks.isEmpty)
    p failure (new RuntimeException("No open tasks"))
  else {
    p success tasks
  }
}

fetchOpenTasks onComplete {
  case Success(tasks) => Notification.sendForTasks(tasks)
  case Failure(e) => UI.inform(e.getMessage)
}
```

Promise can be used to construct Future combinator:
```
def first[T](f: Future[T], g: Future[T]): Future[T] = {
  val p = promise[T]

  f foreach {
    case x => p.trySuccess(x)
  }

  g foreach {
    case x => p.trySuccess(x)
  }

  p.future
}
```

## How to convert blocking function to Future with Promise
Suppose we have a blocking function:
```
def fib(input: Int): BigInt = {
  lazy val fibs: Stream[BigInt] = BigInt(0) #:: BigInt(1) #:: fibs.zip(fibs.tail).map(n => n._1 + n._2)

  val result = for {
    p <- fibs.take(input).zip(1 to input)
    //_ = println(s"fib(${p._2})=${p._1}")
  } yield p._1

  result.last
}
```

Let's see the Future version:
```
def fibFuture(input: Int): Future[BigInt] = {
  val p = Promise[BigInt]
  Future {
    try p success(fib(input))
    catch { case e if NonFatal(e) => p failure(e) }
  }
  p.future
}
```

## How to convert callback to Future with Promise
Suppose we have a function taking a callback to handle the result:
```
def fibCB(input: Int, cb: (BigInt) => Unit): Unit = cb(fib(input))
```

The Future version is:
```
def fibFutureForCB(input: Int): Future[BigInt] = {
  val p = Promise[BigInt]
  Future {
    try fibCB(input, x => p success(x))
    catch { case e if NonFatal(e) => p failure(e) }
  }
  p.future
}
```

## How to cancel asynchronous computation
The key point is to give application a Promise to do cancellation
```
type Cancellable[T] = (Promise[Unit],Future[T])

def cancellable[T](b: Future[Unit] => T): Cancellable[T] = {
  val cancel = Promise[Unit]
  val f = Future {
    val r = b(cancel.future)
    if (!cancel.tryFailure(new Exception))
      throw new CancellationException
    r
  }
  (cancel, f)
}
```

The application could be like this:
```
val (cancel, value) = cancellable { cancel =>
  for (i <- 0 to 5) {
    if (cancel.isCompleted) throw new CancellationException
    Thread.sleep(500)
    println(s"$i: working")
  }
  "result"
}

Thread.sleep(1500)
cancel trySuccess()
println("cancelled!")
Thread.sleep(2000)
```

Once application tells `cancel` to success, the computation will stop

# Reference
Scala's official site has an introduction about [promise and future][1].

[1]: http://docs.scala-lang.org/overviews/core/futures.html#functional-composition-and-for-comprehensions 
