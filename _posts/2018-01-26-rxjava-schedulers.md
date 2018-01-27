---
layout: post
title: RxJava - Schedulers - What, when and how to use it?
excerpt: "Schedulers are one of the main componenets in RxJava. They are responsible for performing operations of Observable on different threads."
categories: [RxJava,Android]
comments: true
image:
  feature: rxJava.png
---
Schedulers are one of the main components in RxJava. They are responsible for performing operations of Observable on different threads. They help to offload the time-consuming onto different threads.
In this post we will learn the types of schedulers and when to use the different types.

* **IO** - This is one of the most common types of Schedulers that are used. They are generally used for IO related stuff. Such as network requests, file system operations.
IO scheduler is backed by thread-pool.
>Java Thread pool represents a group of worker threads that are waiting for the job and reuse many times.

  It starts with creating one worker, which can be re-used for other operations. Of course, if it can't be re-used (in case of long processing jobs), it spawns a new thread (or worker) for handling operation.
  This benefit could also be problematic because it is unbounded and can have a drastic effect on overall performance if a huge number of threads are spawned (though the unused threads are removed after 60 seconds of inactivity). But still IO scheduler is a good scheduler to use. It is available as:
  ```
  observable.subscribeOn(Schedulers.io())
  ```

* **Computation** -  This scheduler is quite similar to IO Schedulers as this is backed by thread pool too. However, the number of threads that can be used is fixed to the number of cores present in the system. So if you have two cores in your mobile, it will have 2 threads in the pool. This also means that if these two threads are busy then the process will have to wait for them to be available. While this limitation makes it a poor fit of IO related things, it is good for performing small calculations and are generally quick to perform operation. It is available as:
```
observable.subscribeOn(Schedulers.computation())
```

* **NewThread** - As the name suggests, it spawns a new thread for each active observable. This can be used to offload time consuming operation from main thread onto other thread. Since it just spawns a new thread whenever it requires, you need to take care of this because thread creation is a costly operation and can have a drastic effect in mobile environment if the number of observables are high enough.

 ```
 observable.subscribeOn(Schedulers.newThread())
 ```

* **Single** - This scheduler is quite simple as it is backed just by one single thread. So no matter how many observables are there, it will run only on that one thread. It can be thought as a replacement to your main thread. It is available as:
```
observable.subscribeOn(Schedulers.single())
```



* **Trampoline** - This scheduler runs the code on current thread. So if you have a code running on the main thread, this scheduler will add the code block on the queue of main thread. It is quite similar to *Immediate* Scheduler as it also blocks the thread, however, it waits for the current task to execute completely(while *Immediate* Scheduler invokes the task right away). Trampoline schedulers come in handy when we have more than one observable and we want them to execute in order.

 ```
 Observable.just(1,2,3,4)
    .subscribeOn(Schedulers.trampoline())
    .subscribe(onNext);
 Observable.just( 5,6, 7,8, 9)
    .subscribeOn(Schedulers.trampoline())
    .subscribe(onNext);

 Output:
    Number = 1
    Number = 2
    Number = 3
    Number = 4
    Number = 5
    Number = 6
    Number = 7
    Number = 8
    Number = 9
 ```


 It is available as:
```
observable.subscribeOn(Schedulers.trampoline())
```
>Immediate scheduler ( present in RxJava 1), just starts the task in a blocking way on the current thread that is active, disregarding which process is currently running

* **Executor Scheduler** - This is more of a custom IO Scheduler. In this scheduler, we can create a custom pool of threads by specifying the size of pool. This can be used in scenarios where number of observables could be huge for IO Schedulers. Below is an example of Executor Scheduler:
```
val	executor	=	Executors.newFixedThreadPool(10);
val	pooledScheduler	=	Schedulers.from(executor);
```

* **Android Scheduler** - This Scheduler is provided by *rxAndroid* library. This is used to bring back the execution to the main thread so that UI modification can be made. This is usually used in *observeOn* method. It can be used by:
```
AndroidSchedulers.mainThread()
```
Also,feel free to checkout Scheduler's documentation on the link:
<a href="http://reactivex.io/RxJava/2.x/javadoc/io/reactivex/schedulers/Schedulers.html#computation">Schedulers Documentation</a>

<a href="https://www.packtpub.com/application-development/kotlin-programming-cookbook">
  <img src="/img/cookbook.png"/>
</a>
