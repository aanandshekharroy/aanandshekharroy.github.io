---
  layout: post
  title: "Started services in Android"
  date: 2019-02-01
  author: Aanand Shekhar Roy
  cover: '/assets/img/started-service.jpg'
  tags: Android
  comments: true
  post_url: started-services-in-android
---
In the last post, we read about [Bound services](http://www.aanandshekharroy.com/articles/2019-01/bound-services-in-android) and how their lifecycle depends on the component which binds to them. 
There is a second kind of service, called started services, whose lifecycle is independent of any component. They may continue to run in the background for an indefinite time.

Due to this indefinite running nature, resulting in poor performance, Android 8.0 brought a lot of restrictions to started services and the Android system will kill a service running service without a foreground UI if they run for a long time.

Android is pushing you to use bound services like JobSchedulers for doing things in the background so that it is more efficient and it results in better performance. 
You should definitely think of using the existing bound services before attempting to create your own.
There are cases where JobScheduler won't help you, like map navigation or music play. In those cases, you should use a foreground service, which is also a started service, but with a UI (through notification). This will ensure that Android doesn't kill it (or atleast until the shit happens).

# Creating a service
All services require you to implement the `onBind` method. Since this isn't a bound service, we are just going to return `null`
``` java
class RandomNumberGeneratorService:Service() {
    override fun onBind(p0: Intent?): IBinder? {
        return null
    }
}
```
# Starting a service
To start a service, we need to use an explicit intent.
```
val startServiceIntent = Intent(this, RandomNumberGeneratorService::class.java).apply {
            action = START_SERVICE_ACTION
        }
startService(startServiceIntent)
```

>On Android 8.0 and higher devices, calling
startService from the background (like from Broadcast receiver) will result in an IllegalStateException. In that case, use startForegroundService and call startForeground
within the Service, within 5 seconds, to start a foreground Service while your
app is in the background. 

Since on Android 8.0 and higher, non-foreground services are likely to get killed, we'll make our service a foreground service. To do that, we'll use the `startForeground` method inside the service. This method takes in two arguments, a notification id (constant) and notification.  

> On Android 9.0, Apps wanting to use foreground services must now request the FOREGROUND_SERVICE permission first. This is normal permission, so the system automatically grants it to the requesting app. Starting a foreground service without the permission throws a SecurityException.

In order to stop a foreground service from within a service, we need to call two methods:

- **`stopForeground`** - This method takes a boolean, whether to remove the notification or not. Passing `true` will remove the notification.

- **`stopSelf`** - This will stop the service and will help the system to recover resources.
There is also an overloaded version of this method, which takes in the `startId`. The overloaded version is helpful if the service handles multiple requsts to `onStartCommand` method concurrently. Stopping the service will terminate the other requests. So to let all the request complete, you should use the `stopSelf(int)`. This way, if you are stopping a service for a `startId`, meanwhile another request with new startId has come in, the service will not stop since the startId doesn't match. 

An over-simplified look to stop the service is:

- **stopSelf()** - Always stops the current service

- **stopSelf(startId)** - Stops the current service, but *only if* startId matches the ID specified the last time the service was started.

- **stopService(Intent)** - Used to stop service from the outside

If you feel that you don't need to hold on to the foreground priority, you can use the `stopForeground` method to remove from the foreground state, while allowing the system to kill it if needed. Note that this doesn't stop the service, it just removes the foreground state of the service.

# Restart behavior of service

The `onStartCommand` is called everytime the service is started using `startService` call so it will be called several times in the Service's lifecycle. You have to provide in this method, how the service should react when restarted, or when the system kills it before completing. There are several flags as listed below :

- **START_STICKY** - This is the standard behavior. In this `onStartCommand` will be called everytime the service restarts, after being killed by the system. Note that in this case, intent passed will be `null` when the system restarts the service. This flag is used if either the service itself manages its lifecycle, or is managed by other components using `startService` and `stopService`.

- **START_NOT_STICKY** - In this mode, the service will be restarted only if there are pending calls of `startService`.

- **START_REDELIVER_INTENT** - In this mode, the service will be restarted if there are pending calls to start, or , if the system terminates the service before the service made a call to `stopSelf`. In that case, last passed intent will be redelivered.

Inside the `onStartCommand`, you can determine which restart mode caused this `onStartCommand` to run. You can know which among the beow two is true: 

- **START_FLAG_REDELIVERY** — This flag means that the intent is redelivered because the service destroyed the service before it could call `stopSelf`.

- **START_FLAG_RETRY** — Indicates that the Service has been restarted after an abnormal termination. It is passed in when the Service was previously set to START_STICKY.