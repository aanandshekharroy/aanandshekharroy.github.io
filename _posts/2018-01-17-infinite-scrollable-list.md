---
layout: post
title: Endless list using Recycler view - Kotlin
excerpt: "Infinite scroll is the most prevalant designs of all times. The reason of it's meteoric rise is that it actually works. In this article, we will learn how to implement one in Android"
categories: [Kotlin,Android]
comments: true
image:
  feature: https://images.unsplash.com/photo-1446716336919-df838e44ce7b?crop=entropy&dpr=2&fit=crop&fm=jpg&h=475&ixjsv=2.1.0&ixlib=rb-0.3.5&q=50&w=1250
  credit: Siyan Ren
  creditlink: https://unsplash.com/photos/5cwPfMLqYs0
---

Infinite scroll is the most prevalant designs of all times. The reason of it's meteoric rise is that it actually works. In this article, we will learn how to implement one in Android.
I'll be creating a very simple app like the below gif. It scrolls upto few number, then fetches another set of data and then is scrollable again. Checkout the below the gif:

![Infinite scroll gif](/img/infinite-scroll.gif)

Let's go through the below steps to create this app:

### Step 1 - Creating layout for list item

Below is the item layout for the list.
##### recycler_row.xml

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:layout_height="wrap_content">

    <TextView
        android:id="@+id/recycler_row_text_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp" />

    <View
        android:layout_width="match_parent"
        android:layout_height="1dp"
        android:layout_alignParentBottom="true"
        android:alpha="0.1"
        android:background="@android:color/black" />
</LinearLayout>
{% endhighlight %}

### Step 2 - Creating Recycler view
Since we are dealing with only one activity, I am putting the Recycler view in that Activity.
##### activity_main.xml

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

</android.support.constraint.ConstraintLayout>
{% endhighlight %}

### Step 3 - Creating Recycler view adapter
To populate data in the recycler view, we will create a recyler view adapter. Here is how it looks:
##### activity_main.xml

{% highlight Java %}
class RecyclerAdapter(val recyclerList: List<Int>) : RecyclerView.Adapter<RecyclerAdapter.ViewHolder>() {
  override fun onBindViewHolder(viewHolder: RecyclerAdapter.ViewHolder, position: Int) {
    viewHolder.bind(recyclerList[position])
  }
  override fun onCreateViewHolder(viewGroup: ViewGroup, position:Int): RecyclerAdapter.ViewHolder {
    val view = LayoutInflater.from(viewGroup.context).inflate(R.layout.recycler_row, viewGroup, false)
    return ViewHolder(view)
  }
  override fun getItemCount(): Int {
    return recyclerList.count()
  }
  class ViewHolder(itemView: View) :RecyclerView.ViewHolder(itemView) {
    val itemTextView :TextView = itemView.findViewById(R.id.recycler_row_text_view)
    fun bind(recyclerItemText: Int) {
      itemTextView.text = recyclerItemText.toString()
    }
  }
}
{% endhighlight %}

*This is emphasized*. Donec faucibus. Nunc iaculis suscipit dui. 53 = 125. Water is H2O. Nam sit amet sem. Aliquam libero nisi, imperdiet at, tincidunt nec, gravida vehicula, nisl. The New York Times (That’s a citation). Underline.Maecenas ornare tortor. Donec sed tellus eget sapien fringilla nonummy. Mauris a ante. Suspendisse quam sem, consequat at, commodo vitae, feugiat in, nunc. Morbi imperdiet augue quis tellus.

HTML and CSS are our tools. Mauris a ante. Suspendisse quam sem, consequat at, commodo vitae, feugiat in, nunc. Morbi imperdiet augue quis tellus. Praesent mattis, massa quis luctus fermentum, turpis mi volutpat justo, eu volutpat enim diam eget metus.

### Blockquotes

> Lorem ipsum dolor sit amet, test link adipiscing elit. Nullam dignissim convallis est. Quisque aliquam.

## List Types

### Ordered Lists

1. Item one
   1. sub item one
   2. sub item two
   3. sub item three
2. Item two

### Unordered Lists

* Item one
* Item two
* Item three

## Tables

| Header 1 | Header 2 | Header 3 |
|:--------|:-------:|--------:|
| cell 1   | cell 2   | cell 3   |
| cell 4   | cell 5   | cell 6   |
|----
| cell 1   | cell 2   | cell 3   |
| cell 4   | cell 5   | cell 6   |
|=====
| Foot 1   | Foot 2   | Foot 3   |

## Code Snippets

{% highlight css %}
#container {
  float: left;
  margin: 0 -240px 0 0;
  width: 100%;
}
{% endhighlight %}

## Buttons

Make any link standout more when applying the `.btn` class.

{% highlight html %}
<a href="#" class="btn btn-success">Success Button</a>
{% endhighlight %}

<div markdown="0"><a href="#" class="btn">Primary Button</a></div>
<div markdown="0"><a href="#" class="btn btn-success">Success Button</a></div>
<div markdown="0"><a href="#" class="btn btn-warning">Warning Button</a></div>
<div markdown="0"><a href="#" class="btn btn-danger">Danger Button</a></div>
<div markdown="0"><a href="#" class="btn btn-info">Info Button</a></div>

## Notices

**Watch out!** You can also add notices by appending `{: .notice}` to a paragraph.
{: .notice}
