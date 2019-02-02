---
  layout: post
  title: "Navigation drawer using Navigation architecture component"
  date: 2019-01-28
  author: Aanand Shekhar Roy
  cover: '/assets/img/navigation-arch-component.png'
  tags: Android
  comments: true
  post_url: navigation-drawer-using-navigation-architecture-component
---
Google release Navigation architecture component in Google I\O'18. While it is still in `alpha` and some say it is far from perfect, it still is pretty awesome and gives us a glimpse of how things coul look like in the future. Navigation architecture components aims to separate the navigation logic from your code, and handles complex things like backsack manageent easily.

In this post, we will learn how to hook the material drawer layout (image below) with the new and shiny navigation architecture component.
![nav_view](/assets/img/nav_view.svg)
>The current version of navigation architecture component is `1.0.0-alpha11`, which means the API may change in future, so make sure to read up the latest documentation when implementing.

Before we move ahead, it'd be a good idea to get familiar with principles of the new navigation architecture component. 

The new navigation architecture basically composes of three main components:

- NavHost - Simply put, this is where the activity hosts the navigation of the app. It is a container where destinations are swapped in and out as a user navigates through your app.

In our case, we'll put the NavHost in our `activity_main.xml` as below:
``` java
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity">

    <android.support.design.widget.AppBarLayout
            android:layout_height="wrap_content"
            android:layout_width="match_parent"
            android:theme="@style/AppTheme.AppBarOverlay">

        <android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                android:background="?attr/colorPrimary"
                app:popupTheme="@style/AppTheme.PopupOverlay"/>

    </android.support.design.widget.AppBarLayout>

    <!--<include layout="@layout/content_main"/>-->
    <fragment
            android:id="@+id/nav_host_fragment"
            android:name="androidx.navigation.fragment.NavHostFragment"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:defaultNavHost="true"
            app:navGraph="@navigation/app_navigation"/>
</android.support.design.widget.CoordinatorLayout>
```
The `app:defaultNavHost="true"` is necessary for NavHostFragment to intercept the system Back button.
Now, we need to provide the navigation graph. The navigation graph in our case looks like:
``` java
<?xml version="1.0" encoding="utf-8"?>
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:app="http://schemas.android.com/apk/res-auto" xmlns:tools="http://schemas.android.com/tools"
            android:id="@+id/app_navigation"
            app:startDestination="@id/main_screen">
        <fragment
                android:id="@+id/main_screen"
                android:name="com.example.theseus.navdraw.main_view.MainFragment"
                tools:layout="@layout/fragment_main">
                <action android:id="@+id/open_cart"
                app:destination="@id/cart_screen">

                </action>
        </fragment>
        <fragment
                android:id="@+id/cart_screen"
                android:name="com.example.theseus.navdraw.cart_view.CartFragment"
                tools:layout="@layout/fragment_cart">
        </fragment>
</navigation>
```