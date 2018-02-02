---
layout: post
title: 'Know your sensors - Android Location Services'
date: 2018-01-29
author: Aanand Shekhar Roy
cover: '/assets/img/location.png'
tags: Android
comments: true
post_url: android-location-services
---
Location services on handheld devices have transformed this world. Several billion dollar companies like Uber exist because of it. Location services provide contextual information keeping in mind your current location. It could be search results, place recommendations, restaurants near you etc.
We call this facility Android Location Services, which consists of different location providers and they have their own strengths and weakness. In this blog, we will assess the types of location providers that are present in Android Location Service and will look at the scenarios when one should be used over other.
## GPS
GPS uses a system of satellites (24 in number) to determine the location of a receiver(Android device). The satellites transmit information to the receiver and receiver use this transmitted information to measure the distance to each satellite by the amount of time it takes to receive a transmitted signal. A minimum of three satellites' distance from the device is needed to determine a 2-D location. Another thing to note is that the system of satellites keeps revolving around the earth, so the satellite should be in the "line of sight" of the device in order for the device to receive the signals.

In order to use this provider, we need to add the below permission in AndroidManifest.xml:
```
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

### Limitation
* Since the device needs to be in the line of sight to the satellites, GPS doesn't work properly if you are indoor or you are at a place where the clear sky is not available (like dense forests).
Also, since the receiver requires more than one satellite to calculate its 2-D location, it can take a  substantial amount of time to calculate it.
Since they are radio-waves, they often get reflected with objects. This happens a lot in urban areas and hence can result in in-accurate calculations by the device.

* GPS location provider relies on a separate radio, it consumes more battery power than other location providers. So if your app remains active for a long time, this can drain your battery.
* Since your device needs to perform calculations based on the signals received, you need to take care of not blocking the app if calculations take longer(in case where it is difficult to receive signals).


## Network Provider
The network location provider can use different ways to determine your location such as cell towers, or wireless network information.
#### Wifi
In case of WiFi, the device tracks which access points it can detect and the current signal strength. Based on that information and WiFi's MAC address, it determines approximate location.
Using Wifi has certain advantages over GPS provider. It consumes less power and it works better in areas where GPS signals aren't very helpful to determine the location. For example, indoors and urban areas where wifi is common but due to density, signals are properly received by the device.

In order to use it, you need to add either of the two lines in your AndroidManifest.xml:
```
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```
Or
```
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```
##### Limitation
* Since the provider needs wifi signals to determine the location, the Wi-Fi networks must be in range.
* Some access points have SSIDs that ends in *_nomap* so their information will not be sent to the location service.
* The Google location services determines the location from Wifi by creaking a network map, so if the location of wifi is changed, the service will determine the wrong location unless other connected devices confirm this change.

#### Cell IDs
Android can also use cellular network (in a similar way as Wi-Fi access points), to determine the location of the device.
This provider works only if the device is connected with a cell tower. The device knows the unique ID of the cell tower and also the IDs of previous towers it was connected to, and based on that,  the Google location service determines the location.

In order to use it, you need to add either of the two lines in your AndroidManifest.xml:
```
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```
Or
```
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```
##### Limitation
* The device must be connected to a cell tower.
* The cell tower must be providing the needed information that is needed to create the network map.

## Passive Location Provider
This provider is quite interesting. Rather than getting location information on its own, the passive location providers get the location update when some other app has requested for it. So it kind of piggy-backs on someone else's data. Since it depends on other's data, it doesn't control the source of the location update, which means, it can either get the data from GPS provider, or from Network provider, or from neither of them(if none of the other apps are receiving location updates). Hence, it is mandatory to have *android.permission.ACCESS_FINE_LOCATION * permission.
Since Passive location provider is not guaranteed to receive any location update at all, hence you shouldn't use it in the app if it is in the foreground. But you can use this provider in the background to keep your app upto date.

| Location Provider | Permission required | Accuracy | Power consumption |
|:--------|:-------:|--------:|
| GPS Provider | *android.permission.ACCESS_FINE_LOCATION* or *android.permission.ACCESS_COARSE_LOCATION*   | Most accurate data (Upto 20ft)   | Highest power consumption|
| Network Provider   | *android.permission.ACCESS_FINE_LOCATION* or *android.permission.ACCESS_COARSE_LOCATION*  | Lesser accurate data than GPS (Upto 5300ft / 1mile)   | Lower battery consumption than GPS|
| Passive Provider   | *android.permission.ACCESS_FINE_LOCATION*| N/A   | N/A|

You can also control the ways by which Google location service determines your location. Checkout the below image of location setting:  

![Location Settings](/assets/img/sensor_settings.jpeg)

In the next few articles, I will go through the implementation of these location providers.
