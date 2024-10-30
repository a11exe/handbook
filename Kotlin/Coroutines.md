# Coroutines

+ [Launch](#launch)
+ [runBlocking vs coroutineScope](#runblocking-vs-coroutinescope)
+ [Cancellation and Timeouts](#cancellation-and-timeouts)
+ [Running Asynchronous Actions Concurrently](#running-asynchronous-actions-concurrently)

## Launch

Coroutines allow us to create asynchronous programs in a fluent way, and they’re based on 
the concept of Continuation-passing style programming.

Suspending functions can be created as standard Kotlin functions, but we need to be aware that we can only call them from within a coroutine. 

Let’s say that we have an expensive computation function that is appending a String to the input list:
```kotlin
suspend fun expensiveComputation(res: MutableList<String>) {
    delay(1000L)
    res.add("word!")
}
```

We can use a launch coroutine that will execute that suspend function in a non-blocking way.

```kotlin
@Test
fun givenAsyncCoroutine_whenStartIt_thenShouldExecuteItInTheAsyncWay() {
    //given
    val res = mutableListOf<String>()

    //when
    runBlocking {
        launch { expensiveComputation(res) }
        res.add("Hello,")
    }

    //then
    assertEquals(res, listOf("Hello,", "word!"))
}
```

`launch` is a coroutine builder. 
It launches a new coroutine concurrently with the rest of the code, which continues to work independently.

`runBlocking` is also a coroutine builder that bridges the non-coroutine world of a regular `fun main()` and the code with coroutines inside of `runBlocking { ... }` curly braces.

In addition to the coroutine scope provided by different builders, 
it is possible to declare your own scope using the `coroutineScope` builder. 
It creates a coroutine scope and does not complete until all launched children complete.

A `launch` coroutine builder returns a Job object that is a handle to the launched coroutine and can be used to explicitly wait for its completion.

```kotlin
val job = launch { // launch a new coroutine and keep a reference to its Job
    delay(1000L)
    println("World!")
}
println("Hello")
job.join() // wait until child coroutine completes
println("Done") 
```

## runBlocking vs coroutineScope

Both `runBlocking` and `coroutineScope` are coroutine builders, which means they are used to launch coroutines, but we use them in different contexts.

When we use `coroutineScope` to build and launch a coroutine, we create a suspension point. 
Suspension points are places in the code where Kotlin may suspend the current coroutine. 
However, we cannot create a suspension point when there is nothing to suspend, 
so **we cannot invoke coroutineScope outside the scope of an existing coroutine**.

The coroutines launched by `coroutineScope` are suspendable. 
To see this, let’s begin by creating a coroutine dispatcher using a fixed thread pool with two threads:
```kotlin
val context = Executors.newFixedThreadPool(2).asCoroutineDispatcher()
```

```kotlin
fun demoWithCoroutineScope() = runBlocking {
    (1..10).forEach {
        launch(context) {
            coroutineScope {
                println("Start No.$it in coroutineScope on ${Thread.currentThread().name}")
                delay(500)
                println("End No.$it in coroutineScope on ${Thread.currentThread().name}")
            }
        }
    }
}
```
The `delay()` method is a suspending function that creates a suspension point for the coroutine launched by coroutineScope.
Despite each coroutine invoking delay() with 500 milliseconds, our total runtime was not much more than that. 
We can also see that some coroutines were started on pool-1-thread-1 but completed on pool-1-thread-2, and vice versa.

Next, let’s try replacing coroutineScope with runBlocking:

```kotlin
fun demoWithRunBlocking() = runBlocking {
    (1..10).forEach {
        launch(context) {
            runBlocking {
                println("Start No.$it in runBlocking on ${Thread.currentThread().name}")
                delay(500)
                println("End No.$it in runBlocking on ${Thread.currentThread().name}")
            }
        }
    }
}
```
Each thread was completed on the same thread it started on. 
The coroutine launched by runBlocking ignored the suspension point created by the delay() call. 
**runBlocking coroutines are not suspendable.**

**Coroutines launched using runBlocking are not cancelable.** 
Since cancellation occurs at suspension points, and runBlocking coroutines are not suspendable and do not have suspension points, the coroutine was allowed to complete its execution.





## Cancellation and Timeouts
Sometimes, after we have triggered some long-running asynchronous computation, we want to cancel 
it because we’re no longer interested in the result.

When we start our asynchronous action with the `launch()` coroutine, we can examine the isActive flag. 
This flag is set to false whenever the main thread invokes the `cancel()` method on the instance of the Job:

```kotlin
@Test
fun givenCancellableJob_whenRequestForCancel_thenShouldQuit() {
    runBlocking<Unit> {
        //given
        val job = launch(Dispatchers.Default) {
            while (isActive) {
                //println("is working")
            }
        }

        delay(1300L)

        //when
        job.cancel()

        //then cancel successfully
    }
}
```

When we’re requesting some processing and are not sure how much time that computation will take, 
it’s advisable to set the timeout on such an action.
If the processing does not finish within the given timeout, we’ll get an exception, and we can react to it appropriately.

```kotlin
@Test(expected = CancellationException::class)
fun givenAsyncAction_whenDeclareTimeout_thenShouldFinishWhenTimedOut() {
    runBlocking<Unit> {
        withTimeout(1300L) {
            repeat(1000) { i ->
                println("Some expensive computation $i ...")
                delay(500L)
            }
        }
    }
}
```

## Running Asynchronous Actions Concurrently

Let’s say that we need to start two asynchronous actions concurrently and wait for their results afterward.

We can leverage the `async()` coroutine to achieve this by starting processing in two separate threads concurrently:

```kotlin
@Test
fun givenHaveTwoExpensiveAction_whenExecuteThemAsync_thenTheyShouldRunConcurrently() {
    runBlocking<Unit> {
        val delay = 1000L
        val time = measureTimeMillis {
            //given
            val one = async(Dispatchers.Default) { someExpensiveComputation(delay) }
            val two = async(Dispatchers.Default) { someExpensiveComputation(delay) }

            //when
            runBlocking {
                one.await()
                two.await()
            }
        }

        //then
        assertTrue(time < delay * 2)
    }
}
```

After we submit the two expensive computations, we suspend the coroutine by executing the `runBlocking()` call. 
Once results one and two are available, the coroutine will resume, and the results are returned.

There are two approaches to making computation code cancellable. 
+ The first one is to periodically invoke a suspending function that checks for cancellation. 
There is a `yield` function that is a good choice for that purpose. 
+ The other one is to explicitly check the cancellation status. `while (isActive)`




