# Previously on Building A Note Taking App

In the [**previous tutorial**](https://androidpirate.github.io/NoteAppRecyclerView/  "**previous tutorial**"), we finished implementing our **RecyclerView** and populated it using static data.



## NoteApp CardView – Tutorial 2

Start by cloning/forking the app from the link below:

[**NoteApp CardView - Tutorial 2**](https://github.com/androidpirate/NoteAppCardView  "**NoteApp CardView - Tutorial 2**")



### Goal of This Tutorial

The goal of this tutorial to polish our UI with **CardView** and add a **click listener** which will respond to user click events.

**CardView** is a container layout element (**A FrameLayout that has rounded corners and a shadow!!!**), which was first introduced in **API level 25 (Lollipop)** as part of the **Support Library**. It is also part
of the material design guidelines, which is a comprehensive guide on how Android applications should look, feel over different platforms.
(Check out [**this site**](https://developer.android.com/design/material/index.html  "**this site**"), for how it is applied to Android applications.)



### What’s in Starter Module?

Starter module already has a fully functional **RecyclerView** which displays static notes provided by **FakeDataUtils class** **(This class name is "an absolute klaas" by the way)**.

You can follow the steps below and give it a shot yourself, and if you stuck at some point, (stay calm and...) check out the **solution module** or the rest of the tutorial.



### Steps to Build

1. Add CardView dependencies to **build.gradle (app)**
2. Add a **CardView** element to **list_item.xml**
3. Remove the **FrameLayout** used as a line separator from **list_item.xml**
4. Define a **listener interface in NoteAdapter class** and add a **onClick() method**
5. Implement **NoteClickListener’s onClick() method** to show a **Toast message** that displays note number when clicked



### Adding App Level Dependencies

**CardView** is part of the **support library** and we need to add the dependencies to our **app level build.gradle file** in order to use it in our app.

Open up your **build.gradle file** and insert the following (or the latest) **CardView** dependency as shown below:


```xml
dependencies {
  .
  .
   implementation 'com.android.support:cardview-v7:27.1.1'
}
```


Once any changes are made to **build.gradle file**, it has to be synced, so don’t forget to **sync it**.



### The Layout

Open up **list_item.xml** layout file and add a new **CardView** element as the parent in the layout file and move  **RelativeLayout and its children** as the **CardView's** children. Don't forget to remove **FrameLayout (line separator)** at the bottom of the original layout:


```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.CardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="110dp"
    app:cardBackgroundColor="@android:color/holo_orange_light"
    app:cardElevation="10dp"
    app:cardCornerRadius="5dp"
    android:layout_margin="16dp">

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <TextView
            android:id="@+id/tv_title"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textColor="@android:color/black"
            android:textSize="18sp"
            android:textStyle="bold"
            android:layout_marginLeft="16dp"
            android:layout_marginRight="16dp"
            android:layout_marginTop="4dp"
            android:layout_marginBottom="4dp"
            tools:text="Note Title"/>

        <TextView
            android:id="@+id/tv_description"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textColor="@android:color/black"
            android:textSize="16sp"
            android:layout_below="@id/tv_title"
            android:layout_marginLeft="16dp"
            android:layout_marginRight="16dp"
            android:layout_marginTop="4dp"
            android:layout_marginBottom="4dp"
            android:ellipsize="marquee"
            android:maxLines="3"
            tools:text="@string/lore_ipsum_text"/>

    </RelativeLayout>

</android.support.v7.widget.CardView>
```


**CardView** uses **app: name space tag** to be able to use its unique built-in parameters, such as **cardElevation, cardCornerRadius and cardBackgroundColor**. (Check out the official documentation about it [**here**](https://developer.android.com/reference/android/support/v7/widget/CardView.html "**here**"))

If you switch to your **Design** tab, you can check out how your list items are displayed with recently added **CardView!** You might already know this trick, but it is possible to use placeholder data for some of the elements, such as a **TextView** using **tools:** namespace tag in the first element of your layout.
**(What you might not know is, what other capabilities it has and how to get best out of it, but of course there is a [document](https://developer.android.com/studio/write/tool-attributes.html "document")  about that.)**



### OnClick() Listener

In order to respond to user clicks, we need to implement **a listener interface**, that will listen to click events on list items and handle them gracefully:


```java
public class NoteAdapter extends RecyclerView.Adapter<NoteAdapter.NoteHolder> {
   // Other fields excluded for simplicity
   // Second, we create a field for the listener interface
   private NoteClickListener listener;

   // First we add a public interface NoteClickListener
   public interface NoteClickListener {
       // And we also define a onClick() method in the interface
       void onClick();
   }

   // In NoteAdapter’s constructor we need to pass a NoteClickListener interface
   public NoteAdapter(List<Note> notes, NoteClickListener listener) {
        this.notes = notes;
        // And assign it here
        this.listener = listener;
    }

   // Rest of the NoteAdapter class is excluded for simplicity
   class NoteHolder extends RecyclerView.ViewHolder {
     // NoteHolder fields are excluded for simplicity
     public NoteHolder(View itemView) {
         super(itemView);
         title = itemView.findViewById(R.id.tv_title);
         description = itemView.findViewById(R.id.tv_description);
         // Here we add a View.OnclickListener to each itemView created
         itemView.setOnClickListener(new View.OnClickListener() {
             @Override
             public void onClick(View v) {
                 // Inside the onClick() method we call the NoteClickListener’s onClick() method
                 listener.onClick();
             }
         });
     }
   }
}
```


First time I have seen such interface and it took me a while to understand what’s going on. (Check out [**this tutorial**](https://antonioleiva.com/recyclerview-listener/ "**this tutorial**") on how to implement a **RecyclerView onClickListener**). What we are doing looks fairly complicated but is actually pretty simple. The **View class** in Android framework, has an interface called **View.OnClickListener** which is described in the [**official documentation**](https://developer.android.com/reference/android/view/View.html "**official documentation**") as:


interface  | View.OnClickListener
------------- | -------------
| Interface definition for a callback to be invoked when a view is clicked.


Which means we can set a **View.OnClickListener** to any view that extends **View class**, and **override the onClick(View v) method** to decide what happens when a view is clicked. In **NoteAdapter**, we defined a public interface called **NoteClickListener and add an onClick() method to it**, **which will be implemented by some other class later on.** For each **itemView**, we set a **View.OnClickListener** in **NoteHolder’s constructor** and **override onClick(View v) method to call NoteClickListener’s onClick() method**. Simply we are delegating handling the click event from one interface to another. **(Dream within a dream within a … You got it!)**



### Responding to Item Clicks

Remember when we implemented NoteAdapter, I mentioned how **NoteClickListener’s onClick() method** will be implemented by another class at some point, here we passed MainActivity as the listener interface to **NoteAdapter’s constructor**. Which means, **we have to implement NoteClickListener interface and onClick() method here in the activity** to decide what happens when we click a list item:


```java
public class MainActivity extends AppCompatActivity
   // Since we passed MainActivity as the listener, we have to implement it
   implements NoteAdapter.NoteClickListener {
   // Fields are excluded for simplicity

   @Override
   protected void onCreate(Bundle savedInstanceState) {
       super.onCreate(savedInstanceState);
       setContentView(R.layout.activity_main);
       .
       .
       recyclerView.setLayoutManager(new LinearLayoutManager(this));
       .
   }

   // Implement one and only onClick() method
   @Override
   public void onClick() {
       // A Toast message that shows which note is click in the list
       Toast.makeText(this, "Note is clicked", Toast.LENGTH_SHORT).show();
   }
}
```


Simply **override to display a Toast message** which shows which item in the list is clicked. **(Of course, there is a way to immediately cancel a Toast message that is being displayed and show another as we keep clicking on items in the list…Figuring that out is delegated to you.)**

And that's it! Run the app and check it out yourself!



### What's in The Next Tutorial

In the next tutorial, we are going to add a new activity to display the details of each note and learn how to switch between activities using **Intents** !!!



### Resources
1. [Android Developer Guides](https://developer.android.com/guide/ "Android Developer Guides") by Google
2. [Antonio Leiva's Personal Website](https://antonioleiva.com) by Antonio Leiva
