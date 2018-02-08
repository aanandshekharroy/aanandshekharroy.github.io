---
layout: post
title: 'Error handling in RxJava/RxKotlin'
date: 2018-02-08
author: Aanand Shekhar Roy
cover: '/assets/img/error-handling.jpg'
tags: Android RxJava Kotlin
comments: true
post_url: error-handling-in-rxjava
---
If you've worked with RxJava/RxKotlin, you might be familiar with three methods of a subscriber. *onNext* , *onError* and *onComplete*. In a reactive stream, elements are consumed by *onNext* first, and *onComplete* is called when the stream ends. If you encounter any error in *onNext*, the complete observable chain is abandoned and control is shifted to *onError* method. So for example, the below code will run perfectly and will provide the output as:

``` java
Observable.fromArray(1,2,3,4)
  .subscribeBy(
      onNext = {
          println(it)
      },
      onComplete = {
          println("Completed")
      },
      onError = {
          println(it.message)
      }
    )
}
```

**Output:**
```
1
2
3
4
Completed
```
Now suppose we encounter an exception in *onNext*, the whole observable chain is dropped. In the below code, we will deliberately induce an exception when the input is 2:

``` java
Observable.fromArray(1,2,3,4)
  .subscribeBy(
        onNext = {
            if(it==2){
                  throw (RuntimeException("Exception on 2"))
            }
            println(it)
        },
        onComplete = {
            println("Completed")
        },
        onError = {
            println(it.message)
        }
  )
```

**Output:**
```
1
Exception on 2
```
This is all well and good if you encounter an error in subscriber, but what if you encounter an error in one of the operators above it? It could be in *map* operator, or in *flatMap* operator. The point is, what to do if you encounter an error before it makes its way to the subscriber?
There are so many things that we can do in RxJava if we encounter an error. The first one we will be talking about is **onExceptionResumeNext()**:
### onExceptionResumeNext
To understand this one, we need to know about **doOnNext** first. You might be thinking, it sounds much like *onNext* of a subscriber. That's right! *doOnNext* is basically for side-effects. The items will go to *doOnNext* before it gets finally consumed by *onNext* method of the observer. So *doOnNext* is a great place for debugging your items in the stream.
Now back to *onExceptionResumeNext*, if we encounter any exception in the observable chain(before they make their way to the observer methods), we can use this method to plug in another observable. Let's have a look at the below code:

``` java
Observable.fromArray(1,2,3)
        .doOnNext {
            if(it==2){
                throw (RuntimeException("Exception on 2"))
            }
        }
        .onExceptionResumeNext(Observable.just(10))
        .subscribeBy(
        onNext = {
            println(it)
        },onError = {
            println(it.message)
        },onComplete = {
            println("Complete")
        }
)
```

**Output:**
```
1
10
Complete
```
In the above code, we deliberately induced an exception when the item is 2 (before it makes it way to get consumed by the observer) in the *doOnNext* and when the exception is triggered we plugged in another observable *Observable.just(10)* and that starts another stream of items, which gets consumed by the previous observer.

One of the use cases of this can be a fallback mechanism. Suppose you want to get the latest news from an API and if there is an error (suppose network connection is down), you can plugin an observable that takes data from your local database.  This way, you are still showing news (even though it is stale).

### onErrorResumeNext
It is quite similar to the *onExceptionResumeNext* but it is for general error. The *onExceptionResumeNext* handles throwables	of	the	 *java.lang.Exception* and *onErrorResumeNext* can handle error of type *java.lang.Throwable* 	and	 *java.lang.Error*
Checkout the below image that describe its working
![onErrorResumeNext](/assets/img/onErrorResumeNext.png)
### doOnError
This is quite similar to *doOnNext* in the way since it can be used to intercept the error before the error makes its way to the consumer. Let's have a look at the below example:
``` java
Observable.fromArray(1,2,3)
        .doOnNext {
            if(it==2){
                throw (RuntimeException("Exception on 2"))
            }
        }
        .doOnError {
            println("Doing on error")
        }
        .subscribeBy(
        onNext = {
            println(it)
        },onError = {
            println(it.message)
        },onComplete = {
            println("Complete")
        }
)
```

**Output:**
```
1
Doing on error
Exception on 2
```  
{% include mailchimp_subscribe.html %}
So as you can see, the block *doOnError* was run before the *onError* of the observer. So it's a great place to do some stuff incase you are about to receive an error. This could include showing toast message or snackbar informing about error.  
### onErrorReturnItem
As the name suggests, it just returns a value if an error is encountered. Let's see the below example:
``` java
Observable.fromArray(1,2,3)
        .doOnNext {
            if(it==2){
                throw (RuntimeException("Exception on 2"))
            }
        }
        .onErrorReturnItem (-1)
        .subscribeBy(
        onNext = {
            println(it)
        },onError = {
            println(it.message)
        },onComplete = {
            println("Complete")
        }
)
```  
**Output:**
```
1
-1
Complete

```
As you can see, after encountering an error, it returns "-1". The placing of *onErrorReturnItem* is crucial. It needs to be downstream from where the error occurs.So in the above example, if you had placed *onErrorReturnItem* before *doOnNext*, it wouldn't have returned the "-1".  
### onErrorReturn
Sometimes, you need to produce the default item (in case you encounter an error) dynamically. So *onErrorReturn* provides you *throwable* and a lambda, which you can use to determine the value you want to return.  
``` java
Observable.fromArray(1,2,3)
        .doOnNext {
            if(it==2){
                throw (RuntimeException("Exception on 2"))
            }
        }
        .onErrorReturn{
            t: Throwable ->
            if(t.message=="<something you want>"){
                1 // Return a value based on error type
            }else{
                100 // Return another value based on different error type
            }
        }
        .subscribeBy(
        onNext = {
            println(it)
        },onError = {
            println(it.message)
        },onComplete = {
            println("Complete")
        }
)
```
Similar to *onErrorReturnItem*, position of *onErrorReturn* also matters. It needs to be downstream of the error in order to fall into *onErrorReturn* lambda.
### retry()
This is another way of error handling. It simply resubscribes the preceding Observable. You need to take care while using this operator. Let's take a previous example here with *retry* operator:
``` java
Observable.fromArray(1,2,3)
        .doOnNext {
            if(it==2){
                throw (RuntimeException("Exception on 2"))
            }
        }
        .retry()
        .subscribeBy(
        onNext = {
            println(it)
        },onError = {
            println(it.message)
        },onComplete = {
            println("Complete")
        }
)
```
**Output:**
```
1
1
1
1
....(infinite loop)
```
As you can see, it went into an infinite loop since the *retry* operator is resubscribing to the Observable which is getting an exception at "2".
![retry](/assets/img/retry.png)

To overcome this situation, you can also provide a number and the *retry* attempt will be reduced to that fixed number. It can be done as below:
``` java
Observable.fromArray(1,2,3)
        .doOnNext {
            if(it==2){
                throw (RuntimeException("Exception on 2"))
            }
        }
        .retry(3)
        .subscribeBy(
        onNext = {
            println(it)
        },onError = {
            println(it.message)
        },onComplete = {
            println("Complete")
        }
)
```
Doing this will reduce the retry attempt to 3.
There are other variations like *retryUntil*, *retryWhen* which are quite similar so you can read more on that at <http://reactivex.io/documentation/operators/retry.html>
