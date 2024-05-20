---
layout: post
title:  "Breaking Free: How Virtual Threads Transform Blocking Code into Non-Blocking Magic"
date:   2024-05-11 10:30:00
categories: microservice
tags: kotlin, java, microservice, technology, database, concurrency, coroutines, multi-threading, virtual-threads
image: /assets/article_images/2024-05-11-coroutines-and-deadlocks/header.png
author_name: Viktor Gottfried
author_link: /authors/viktor-gottfried
author_image: /assets/images/authors/viktor-gottfried/thumbnail.jpg
---


You're driving through a busy city intersection at rush hour. 
Cars are inching forward, everyone trying to get to their destination. 
But then, it happens—one car gets stuck, blocking another, which in turn blocks another. 
Before you know it, the entire intersection is gridlocked, and no one can move. 
You’re stuck in a classic deadlock, wondering how a smooth commute turned into a standstill.

In a multi-threaded environment, a similar gridlock can occur when one thread tries to access a resource that is currently held by another thread. 
Meanwhile, that other thread needs access to a second resource that is already acquired by the first thread. 
This circular dependency causes both threads to be stuck indefinitely, leading to a frustrating deadlock where progress comes to a complete halt.

# Introduction

In our current project, we've chosen a tech stack that includes [Ktor](https://ktor.io/), and [Exposed](https://jetbrains.github.io/Exposed/home.html). 
This combination has generally served us well, offering a powerful way to build and manage our application. 

Ktor is a modern, asynchronous framework developed by JetBrains specifically for building Kotlin applications. 
Designed with coroutines at its core, Ktor enables developers to create scalable and efficient server-side applications and microservices. 
Its non-blocking nature leverages Kotlin’s capabilities for asynchronous programming, making it well-suited for handling a high volume of connections with minimal resource usage.
Ktor's simplicity and flexibility, combined with its powerful feature set, make it an excellent choice for developers looking to build robust web applications using Kotlin.

Exposed is an ORM library developed by JetBrains, tailored specifically for Kotlin. 
It provides a fluent and idiomatic Kotlin API for interacting with databases through SQL. 
Since Exposed is built on top of JDBC, it inherits a blocking design. 
This means that database operations performed through Exposed inherently block the executing thread until the operation completes, which is a traditional approach to handling database interactions in many server-side applications.

# Coroutine

A coroutine is a concurrency design pattern used in programming to make asynchronous programming cleaner and more manageable. 
In Kotlin, coroutines allow developers to write asynchronous code that appears sequential, enhancing readability and maintainability. 
Unlike threads that are managed by the operating system and can be resource-intensive, coroutines are lightweight and managed by the Kotlin runtime. 
A key feature of coroutines is their non-blocking execution; when a coroutine is suspended to wait for an operation to complete, the thread executing the coroutine becomes available to perform other tasks. 
This efficient use of threads enables high scalability for applications, particularly useful in tasks involving waiting, such as network calls or database operations.

Here’s a simple example of how a coroutine can be used in Kotlin to perform an asynchronous task, such as fetching data from the internet.

```kotlin
fun main() = runBlocking {
    // Launch a new coroutine in the background and continue
    launch {
        delay(1000L) // non-blocking delay for 1 second (simulating a long-running task)
        println("World!")
    }
    // Main thread continues while the coroutine is delayed
    println("Hello,") // This line is executed first
}
```

Resulting output:
```bash
Hello,
World!
```

Although it seems counterintuitive to use a blocking call in a discussion about non-blocking code, runBlocking serves a specific purpose. 
It acts as a bridge allowing non-coroutine code to interact seamlessly with libraries that are designed using coroutines, making it useful primarily for main functions and tests

## Executing Blocking Code with Coroutines

Although coroutines are non-blocking, there are situations where you need to call blocking code, such as database operations or network requests that do not support asynchronous APIs. 
To prevent blocking the main thread or the coroutine context threads, you can use the Dispatchers.IO context. This dispatcher is optimized for offloading blocking IO tasks to a shared pool of threads.

```kotlin
// Launch a new coroutine in the IO context for blocking operations
withContext(Dispatchers.IO) {
    val result = performBlockingOperation()
    println("Result: $result")
}

// Simulates a blocking operation
fun performBlockingOperation(): String {
    Thread.sleep(2000) // Simulates a long-running blocking task
    return "Operation Complete"
}
```

If you are not familiar with Kotlin, you may ask what is the difference between `Thread.sleep(...)` and the suspending function `delay(...)`. 
While `Thread.sleep` actually blocks the thread and makes it unresponsive (the thread is literally sleeping and doing nothing), 
`delay` is a suspending function that suspends the coroutine and releases the executing thread, making it available to perform other work. 
Eventually, the coroutine is rescheduled, and a thread is assigned to resume its execution. 
With coroutines, we do not know which thread will execute it when it resumes. 
This makes coroutines much more efficient and scalable for handling asynchronous tasks.

# Exposed and Coroutine

Exposed provides a mechanism intended to bridge its inherently blocking JDBC operations with Kotlin's non-blocking coroutines, 
aiming to streamline integration in coroutine-based environments. 
While Exposed uses JDBC under the hood, which is blocking by design, it offers utilities to adapt to Kotlin's asynchronous programming model.

Here’s how you can transition from a traditional transaction to one that is better integrated with coroutines:

```kotln
// Traditional blocking transaction
val count = transaction {
    FooTable.selectAll().count()
}

// Coroutine-friendly
val count = newSuspendedTransaction(Dispatchers.IO) {
    FooTable.selectAll().count()
}
```

While this feature facilitates the use of coroutines by superficially adapting to their syntax and style, 
it does not alter the underlying blocking nature of database operations. 
This approach, while seemingly effective, can be somewhat misleading as it merely cosmetizes the blocking behavior rather than truly converting it to non-blocking asynchronous operations.

# Understanding the Risks of Mixing Exposed with Coroutines

When integrating Exposed with Kotlin's coroutines through `newSuspendedTransaction`, 
there are significant considerations to keep in mind regarding coroutine suspension and resource management, particularly with database connections.

The use of `newSuspendedTransaction` allows us to write code that appears non-blocking by suspending the coroutine at specific points instead of blocking the thread. 
This is generally beneficial for resource utilization, but it introduces complexities when combined with JDBC transactions. 
The Kotlin documentation highlights that coroutine suspension can occur at any suspension point, 
which means that anytime a suspending function is called within the transaction block, 
the coroutine might be suspended.

```kotlin
val count = newSuspendedTransaction(Dispatchers.IO) {
    FooTable.selectAll().count()
    delay(100) // Simulating a delay or some asynchronous operation
}
```

In the example above, `delay(100)` is a suspending function. 
When this line is executed, the coroutine handling this block of code is suspended, releasing the thread to perform other tasks. 
This behavior becomes problematic due to how JDBC and database connections operate.

## Scenario Leading to Deadlock

JDBC operations are inherently blocking. 
When a transaction is initiated with JDBC, it holds onto a database connection from a connection pool until the transaction is complete.
Connection pools, typically configured with a maximum number of connections (default around 10), are used to manage these expensive resources efficiently.

Consider a scenario where you're handling a high volume of simultaneous requests that interact with the database:

1. Let's say there are 100 simultaneous requests and a Dispatchers.IO context configured with 64 threads. 
These threads begin processing the requests, each requiring a database connection to perform the transaction.
2. As the first 10 coroutines, processing the requests, grab connections and proceed, they hit a suspend function and suspend. The threads are freed up and pick up the next set of requests.
3. Eventually, all available database connections are allocated, but they're held by suspended coroutines that cannot proceed because there are no threads available to continue their execution. 
At the same time, no additional connections are available for new coroutines that are scheduled, leading to a deadlock. The threads are idle, waiting for connections to free up, and the connections are locked in suspended transactions that can't proceed without a thread.

This situation outlines a classical deadlock scenario where both resources (threads and database connections) are fully utilized but in a state that prevents any further progress.



## Conclusion of Understanding the Risks of Mixing Exposed with Coroutines

So, how do we overcome the deadlock issue? 

One possible workaround would be to increase the number of database connections to exceed the maximum number of Dispatcher.IO threads. 
However, this approach is costly and inefficient, as the number of database connections provided by the databases is limited. 
This becomes especially problematic in environments with multiple replicas of your application, 
which would exponentially increase the required connections. 
Furthermore, our physical database is shared by multiple services, making the necessary number of connections unfeasible.

Another idea was to avoid calling suspending functions within a coroutine when using Exposed. 
However, this brings up an important question: Why use coroutines with Exposed at all if you can't utilize the feature to call other suspending functions within a transaction? 
This limitation highlights a fundamental challenge with the current design: it doesn't allow a feasible solution for fully leveraging coroutines in database operations.

It was at least the case until the introduction of the Java Loom Project and Java Virtual Threads. 
These advancements offer a new way to handle concurrency and could potentially address the issues we faced. 

# Virtual Threads to the Rescue

The work on Java Project Loom started in 2017 with the goal of revolutionizing how concurrency is handled in Java. 
A key feature of Project Loom are Virtual Threads, which became production-ready with JDK 21. 
To appreciate the impact of Virtual Threads, let's first review the traditional threading model in Java:

Java Platform threads are mapped 1:1 to OS threads, making them expensive objects. 
Kotlin coroutines optimize thread usage by being lightweight and non-blocking, requiring far fewer Platform Threads. 
However, the challenge persists with blocking code, where we offload these tasks to a large number of threads, hoping there are enough threads to handle the load efficiently.

This is where Virtual Threads come into play. Virtual Threads are also very lightweight. 
They are executed by Carrier Threads, which are mapped 1:1 to OS threads. 
The key advantage of Virtual Threads is that they are non-blocking. 
The Java developers retrofitted the `java.io` and `java.util.concurrent` packages so that whenever a Virtual Thread encounters blocking code, 
it is automatically suspended, and the Carrier Thread is released. 
This is a game changer in the Java world because it allows legacy blocking applications to become non-blocking with minimal adaptation.

However, there are still some constraints: you cannot use the `synchronized` block with Virtual Threads, 
as it will still block the Virtual Thread. Therefore, it's important to review your code and any third-party libraries for compatibility.

Most well-known Java libraries are now Virtual Thread-ready. 
Spring-Boot delivered Virtual Thread support with the 3.2 release. 

The PostgreSQL Driver has been updated to support Virtual Threads a while ago. 
This allows us to offload previously blocking database access to Virtual Threads, 
transforming these operations into non-blocking tasks and resolving the deadlock issue without changes to the application logic.


```
val VIRTUAL_THREAD_DISPATCHER = Executors.newVirtualThreadPerTaskExecutor().asCoroutineDispatcher()

val count = newSuspendedTransaction(VIRTUAL_THREAD_DISPATCHER) {
    FooTable.selectAll().count()
}
```

That's it. By introducing the Virtual Thread Dispatcher context and applying it, we transformed our blocking code into responsive, non-blocking code.
For a detailed example that illustrates both the problem and the solution, check out our project on GitHub: [Example Project](https://github.com/sw-code/swcode-samples/tree/main/virtual-threads/deadlocks).

# Conclusion

Navigating the complexities of integrating Exposed with Kotlin coroutines revealed significant challenges, particularly around deadlocks and blocking operations. 
Traditional solutions, like increasing database connections, proved costly and inefficient. 
However, the introduction of Java Virtual Threads from Project Loom offers a transformative solution. 
Virtual Threads allow us to handle concurrency efficiently, eliminating deadlocks and making our application fully non-blocking without significant changes to our logic.

With Virtual Threads, creating non-blocking applications in Java has never been easier. 
If you’ve ever worked with reactive frameworks like Reactor, you know the struggle of writing non-blocking code and dealing 
with the framework-specific language that often seeps into your business logic. 
Virtual Threads, combined with Kotlin coroutines, offer a dream team of non-blocking code execution and structured concurrency. 
This combination simplifies the development process, allowing you to write clean, efficient, and maintainable code.

By leveraging these advancements, we can build more responsive, resilient, and scalable applications. 
Java developers now have the tools to transform their traditional blocking applications into modern, reactive systems with minimal effort. 
Embrace Virtual Threads and Kotlin coroutines, and take your applications to new heights of performance and reliability.