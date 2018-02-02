---
  layout: post
  title: "Creating web's slot machine a.k.a Infinite list in Android"
  date: 2018-01-17
  author: Aanand Shekhar Roy
  cover: 'https://images.unsplash.com/photo-1446716336919-df838e44ce7b?crop=entropy&dpr=2&fit=crop&fm=jpg&h=475&ixjsv=2.1.0&ixlib=rb-0.3.5&q=50&w=1250'
  tags: Kotlin Android
  comments: true
  post_url: infinite-scrollable-list
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
##### RecyclerAdapter.kt

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


### Step 4 - Create function that provides data
Let's now create a function which when called, appends 30 data items to an existing list. We are emulating a network call, which brings in an additional data and appends it to an existing data-source.


{% highlight Java %}
fun updateDataList(dataList: MutableList<Int>) : List<Int> {
  kotlin.repeat(30) {
    dataList.add(dataList.size + 1)
  }
  return dataList
}
{% endhighlight %}
<a href="https://www.packtpub.com/application-development/kotlin-programming-cookbook">
  <img src="/img/cookbook.png"/>
</a>
*Find this and lot of other recipes written in Kotlin in my book Kotlin Programming Cookbook*

### Step 5 - Setting up the RecyclerView in the activity
Let's now create a function which when called, appends 30 data items to an existing list. We are emulating a network call, which brings in an additional data and appends it to an existing data-source.


{% highlight Java %}
class MainActivity : AppCompatActivity() {
  val dataList = mutableListOf<Int>()
  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    val layoutManager = LinearLayoutManager(this)
    val adapter = RecyclerAdapter(recyclerList =
      updateDataList(dataList))
    recyclerView.layoutManager = layoutManager
    recyclerView.adapter = adapter
    recyclerView.addOnScrollListener(object :
        RecyclerView.OnScrollListener() {
          override fun onScrolled(recyclerView: RecyclerView, dx:
            Int, dy: Int) {
              super.onScrolled(recyclerView, dx, dy)
              if (!recyclerView.canScrollVertically(1)) {
                onScrolledToBottom();
              }
            }
            fun onScrolledToBottom() {
              val initialSize = dataList.size
              updateDataList(dataList)
              val updatedSize = dataList.size
              adapter.notifyItemRangeInserted(initialSize,updatedSize)
            }
            })
          }
        }
{% endhighlight %}

Since we have to intercept when the user has reached the bottom of the list, we have added
a *ScrollListener* . We have overridden the *onScroll* method, and we are using
the *canScrollVertically* method. The *canScrollVertically* method was added in
API level 14, and it is used to check whether this view can be scrolled vertically in a certain
direction. It takes an integer argument (negative to check scrolling up and positive to check
scrolling down) and returns a boolean ( true if possible, false if not). In our example, we
have supplied a positive integer, which will return true if the view can be scrolled down
and false if it can't be. If it can't be further scrolled down (meaning that the data is
exhausted), we will add data to the list and update the list by calling
the *notifyItemRangeInserted* method of the adapter.

##### Checkout the full source code in the below link:
[Source code](https://gitlab.com/aanandshekharroy/Anko-examples/tree/6-endless-list-using-recycler-view) .
