---
layout: post
title: RxJava - Flowables - What, when and how to use it?
excerpt: "World is moving towas event-driven programming. This provides us a way around of polling. The basic concept of Event driven programming is that you observe a resource(called observable), and when the data is ready, the observable notifies the observer. In this article, we will discuss Flowable, which is quite like observable, but has a different purpose."
categories: [RxJava,Android]
comments: true
image:
  feature: rxJava.png
---

To understand Flowables, we need to understand Observables first. Observables are those entities which we observe for any event. Observables are used when we have relatively few items over the time and there is no risk of overflodding consumers.
If there is a possiblity that the consumer can be overflodded, then we use Flowable. One example could be getting huges amount of data from a sensor. They typically psh out data at a high rate. In previous version of RxJava, this overflooding could be prevented by applying backpressure. But in RxJava 2, the development team has separated these two kind of producers into two entities. i.e. Observale and Flowables. According to documentation:


>    A small regret about introducing backpressure in RxJava 0.x is that instead of having a separate >    base reactive class, the Observable itself was retrofitted. The main issue with backpressure is >    that many hot sources, such as UI events, can't be reasonably backpressured and cause unexpected >    MissingBackpressureException (i.e., beginners don't expect them).

>    We try to remedy this situation in 2.x by having io.reactivex.Observable non-backpressured and the >    new io.reactivex.Flowable be the backpressure-enabled base reactive class.

Let's understand the use of FLowable using another example. Suppose you have a source that is emitting data items at a rate of 1 Million items/second. The next step is to make network request on each item. Suppose the device can handle 100 network requests/second. Do you see the problem? The second step is the bottleneck because device can handle atmost 100 requests/second and so the huge amount of data from step 1 will cause **OOM(Out Of Memory)** exception.
The below code is a perfect example of that:
```
  val observable = PublishSubject.create<Int>()
  observable.observeOn(Schedulers.computation())
            .subscribeBy (
                onNext ={
                  println("number: ${it}")
                },onError = {t->
                  print(t.message)
                }
            )
    for (i in 0..1000000){
        observable.onNext(i)
    }
```
In these scenarios, we need **backpressuring** , which in simple words is just a way to handle the items that can't be processed. In the below code, we will handle the case using Flowable:
```
val observable = PublishSubject.create<Int>()
    observable
            .toFlowable(BackpressureStrategy.MISSING)
            .observeOn(Schedulers.computation())
            .subscribeBy (
                onNext ={
                    println("number: ${it}")
                },onError = {t->
                print(t.message)
            }
            )
    for (i in 0..1000000){
        observable.onNext(i)
    }
```
If you run the above code, you'll see the output:
```
Queue is full?!
```
This is because we haven't specified any BackpressureStrategy, so it falls back to default which basically buffers upto 128 items in the queue. Hence the output *Queue is full*

<a href="https://www.packtpub.com/application-development/kotlin-programming-cookbook">
  <img src="/img/cookbook.png"/>
</a>

There are a lot of other backpressuring strategy which we will cover now:
* **Dropping** : What do you do when you can't handle too many things? You drop it. This Backpressuring strategy does the exact same thing. It drops the items if it can't handle more than it's capacity i.e. 128 items (size of buffer)
  There are two ways to apply this Backpressuring strategy:

  ```
  observable.toFlowable(BackpressureStrategy.DROP)
  ```

  Or

  ```
  observable.toFlowable(BackpressureStrategy.MISSING).onBackpressureDrop()  
  ```

* **Preserve the last item** : If the producer sees that the downstream can't cope up with the flow of items, it stops emitting it and waits till it becomes available. In the mean time, it keeps dropping
 the items except the last one that arrived and sends the last one when the downstream is available again.

  There are two ways to apply this Backpressuring strategy:
  ```
  observable.toFlowable(BackpressureStrategy.LATEST)
  ```

  Or

  ```
  observable.toFlowable(BackpressureStrategy.MISSING).onBackpressureLatest()
  ```

  Another variant that is most commonly used in Android world is *debounce*. This is generally used on button clicks where we don't want users to continously press the button while the action of button press is processed. Think of 'Sign in' button, when user clicks on it, we make a network request to the server. We don't want the users to continously keep pressing the button.
  Using the debounce, it takes the last value after a specified time. In the below example, it takes the last value emitted after 1 second:
  ```
  observable.toFlowable(BackpressureStrategy.MISSING).debounce(1000,TimeUnit.MILLISECONDS)
  ```
* **Buffering** : It might not be the best way to handle a lot of emissions, but certainly is a way that is available. In this, you can save the items in a buffer. In this case, items are stored in the buffer till they can be processed.
  There are two ways to apply this Backpressuring strategy:

  ```
  observable.toFlowable(BackpressureStrategy.BUFFER)
  ```

  Or

  ```
  observable.toFlowable(BackpressureStrategy.MISSING).onBackpressureBuffer()
  ```

  You can also specify the buffer size as:
  ```
  observable.toFlowable(BackpressureStrategy.MISSING).buffer(10)
  ```
