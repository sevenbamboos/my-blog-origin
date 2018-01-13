---
title: Async in action
categories: Programming
date: 2018-01-07 10:13:33
tags:
---


# Multithreading, Parallel and Async

Multithreading
> A form of concurrency that uses multiple threads of execution. 

`Thread`, `BackgroundWorker`, low-level, best to start with high-level abstractions like thread pool.

Parallel
> Divide work among multiple threads that run concurrently.

Maximize the use of multiple processors of CPU. In most cases, work against built-in parallelism on server-side.

Async
> Use futures or callbacks to avoid unnecessary threads.

<!-- more -->

# Async with callback

```
function doTaskAsync(msg, cb) {
	console.log(msg);
	setTimeout(cb, 0);
}

doTaskAsync("a", function() {
	console.log("b");
	doTaskAsnyc("c", function() {
		console.log("d");
	});
});
console.log("e");

// a -> e -> b -> c -> d
```

Drawbacks:
1. Not sequential
2. Inversion of control
Callback executed too early/late, too few/many times, swallow errors, ...

# Async with Promise

```
function doTaskAsync(msg) {
	console.log(msg);
	return new Promise( function(resolve, reject) {
		setTimeout(resolve, 0);		
	});
}

doTaskAsync("a")
.then( function () {
	console.log("b");
	return doTaskAsync("c");
})
.then( function () {
	console.log("d");
})
.catch( function () {...} );

console.log("e");

// a -> e -> b -> c -> d 
```

# Async in ES7 (C# 5.0)

```
async function doTasks() {
	try {
		await doTaskAsync("a");
		console.log("b");
		await doTaskAsync("c");
		console.log("d");
	}
	catch (err) {
		...
	}
	console.log("e");
}
doTasks();

// a -> b -> c -> d -> e

```

# Async in Scala 

(see Promise and Future)

# Async in Java

## Java 5

`interface java.util.concurrent.Future<T>::get()` is a blocking method.

## Java 8

`interface java.util.concurrent.CompletionStage<T>` is Promise

`class java.util.concurrent.CompletableFuture<T> implements Future<T>, CompletionStage<T>`

That's all.

# Async and Reactive

```
Rx.Observable.fromEvent(..., "event a")
	.do(x => console.log("b"))
	.switchMap(x => Rx.Observable.fromEvent(..., "event c"))
	.do(x => { console.log("d"); console.log("e"); }); 
```
