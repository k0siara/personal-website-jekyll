---
title: 'Android ListView VS. RecyclerView'
date: 2021-05-30
permalink: /blog/android-listview-vs-recyclerview/
tags:
  - cool posts
  - category1
  - category2
---

Scrollable list is one of the most common feature in modern mobile apps. In Android we have two popular ways to achieve this. We can use either [**ListView**](https://developer.android.com/reference/android/widget/ListView)or [**RecyclerView**](https://developer.android.com/jetpack/androidx/releases/recyclerview). Do you know exactly how they work and which one should you choose for your projects? Maybe you’re struggling with understanding when to use each of them. Let me help you with that. This article is gonna cover all of these questions and even more!

## ******One last thing before we start******

### ✉️ Android Dev Newsletter

If you enjoy learning about Android like I do and want to stay up to date with the latest, worth reading articles, programming news and much more, consider subscribing to my newsletter👇
[https://androiddevnews.com](https://androiddevnews.com/)

### 🎙 Android Talks Podcast

If you’re a Polish speaker and want to listen to what I have to say about Android, architecture, security and other interesting topics, check out my podcast👇
[https://androidtalks.buzzsprout.com](https://androidtalks.buzzsprout.com/)

💡

Fun fact

**ListView **vs **RecyclerView **is one of the most common questions you can be asked at an Android Dev Interview, so it is worth to read this article and to test both of them on your own to check out what they can do

## Availability

The first key difference between the [**ListView **](https://developer.android.com/reference/android/widget/ListView)and [**RecyclerView **](https://developer.android.com/jetpack/androidx/releases/recyclerview)is when where they introduced. Well, [**ListView **](https://developer.android.com/reference/android/widget/ListView)has been with us since the very beginning (API 1). It was always a way for Android Developers to display scrollable lists. It wasn’t the best solution though, because the list had to create a new view item every time user scrolled and as you know view creation is a very expensive thing to do. The bigger the list, the more resource hungry would the application be. It was a pain in the a** for Android Developers before [**RecyclerView**](https://developer.android.com/jetpack/androidx/releases/recyclerview)came out.

---

**NOTE**

You could implement [**ViewHolder **](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ViewHolder)in the [**ListView **](https://developer.android.com/reference/android/widget/ListView)and reuse the views like in [**RecyclerView**](https://developer.android.com/jetpack/androidx/releases/recyclerview), but it was more like optional thing to do, while in [**RecyclerView **](https://developer.android.com/jetpack/androidx/releases/recyclerview)it’s the default way of writing the adapter class.

---

### Developer’s redemption

In 2014, after a long time of waiting, [**RecyclerView**](https://developer.android.com/jetpack/androidx/releases/recyclerview), [**CardView **](https://developer.android.com/jetpack/androidx/releases/cardview)and **Design Support Library** were introduced. The idea of [**RecyclerView **](https://developer.android.com/jetpack/androidx/releases/recyclerview)was very simple. Instead of creating a new view every time user scrolled to the next positions, just create views once and recycle/reuse them. This was achievable by using [**ViewHolder**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ViewHolder). Besides that, there is also [**LayoutManager**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.LayoutManager), [**ItemDecoration**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ItemDecoration), [**ItemAnimator**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ItemAnimator)and more. Let’s talk in more detail about them…

## So what’s RecyclerView exactly?

[**RecyclerView**](https://developer.android.com/jetpack/androidx/releases/recyclerview)is a library for list drawing that essentially provides a fixed-size window to load a large dataset into. When the views get out of scope it **recycles** the views created before. It is a standalone support library, so in order to use it in your project you have add it separately to your app module’s **build.gradle** file. As of 2021 it is a part of Jetpack, so all of the info about how to include it in your project can be found here: [https://developer.android.com/jetpack/androidx/releases/recyclerview](https://developer.android.com/jetpack/androidx/releases/recyclerview)

### ViewHolder

A [**ViewHolder **](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ViewHolder)describes an item view and metadata about its place within the [**RecyclerView**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView). It allows making our list scroll smoothly by storing views references. That’s why it’s called a [**ViewHolder**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ViewHolder). By doing this calling [**findViewById(int id)**](https://developer.android.com/reference/android/view/View#findViewById%28int%29)occurs much less times, instead of calling it for the entire dataset on each view binding. It can be also implemented in [**ListView**](https://developer.android.com/reference/android/widget/ListView), but it’s not mandatory. The [**RecyclerView’s**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView)adapter forces us to use the [**ViewHolder**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ViewHolder)pattern. The adapter actions are separated to creation — [**onCreateViewHolder(…)**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter#createViewHolder%28android.view.ViewGroup,%20int%29) and to updating the view — [**onBindViewHolder(…)**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter#onBindViewHolder%28VH,%20int%29). If you implement the adapter for [**RecyclerView**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView)then it is compulsory to provide a [**ViewHolder**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ViewHolder)**.** In [**ListView**](https://developer.android.com/reference/android/widget/ListView)it’s optional, but not using it causes inefficient scrolling and much higher memory usage.

### LayoutManager

[**LayoutManager**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.LayoutManager)is responsible for laying out the row views. It allows us to choose how the views should be displayed and scrolled. Besides that it is telling the **ViewHolder** when to recycle a child view once it’s out of scope.

When using a [**ListView**](https://developer.android.com/reference/android/widget/ListView), you can only create a vertical-scrolling list, so it’s not really a flexible solution for us. If you wanted to create a grid before [**RecyclerView**](https://developer.android.com/jetpack/androidx/releases/recyclerview) was introduced, you had to use another UI component for that — [**GridView**](https://developer.android.com/reference/android/widget/GridView).

Now, that we have the **RecyclerView** everything is easier. There are 3 main **LayoutManagers** you can choose from:

[**LinearLayoutManager**](https://developer.android.com/reference/androidx/recyclerview/widget/LinearLayoutManager) — handles vertical and horizontal linear layouts
![](__GHOST_URL__/content/images/max/800/0-As_J0Sewkj76bpaF.png)
[**GridLayoutManager**](https://developer.android.com/reference/androidx/recyclerview/widget/GridLayoutManager) — for managing grid layouts with columns/spans
![](__GHOST_URL__/content/images/max/800/0-UaTiIgVvjPAHTUs1.png)
[**StaggeredLayoutManager **](https://developer.android.com/reference/androidx/recyclerview/widget/StaggeredGridLayoutManager)— for handling grids, but each item can have different size
![](__GHOST_URL__/content/images/max/800/0-uSmrbbj0Df1e7tyF.png)
## Adapter

The most important thing about the [**RecyclerView**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView)is the [**Adapter**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter). Like any other adapters it’s responsibility is to bind datasets to views that are going to be displayed in the window. A typical adapter is just an iterator that has **getCount()** method for knowing about limitations. Usually it depends on the dataset size to create new views for binding. This kind of adapters are also used in [**ListView**](https://developer.android.com/reference/android/widget/ListView)or [**ViewPager**](https://developer.android.com/reference/androidx/viewpager/widget/ViewPager) for example.

[**RecyclerView**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView), as mentioned before, uses the [**ViewHolder **](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ViewHolder)pattern by default. In this case the [**RecyclerView**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView)adapter is not creating new views every time, but instead it creates only [**ViewHolders**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ViewHolder)that hold the inflated views to reuse them later.

---

**NOTE**

[**ViewHolders**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ViewHolder)are created and referenced with specific item **viewType (Int)**, not the position. Because of that it is easier for the adapter to find views to reuse and you can add as many view types as you want, by creating new [**ViewHolder **](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ViewHolder)type classes.

---

The [**onBindViewHolder(…)**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter#onBindViewHolder%28VH,%20int,%20java.util.List%3Cjava.lang.Object%3E%29) method is used to bind views every time a new list element will appear while scrolling, so it will be called a lot of times during scrolling the list, so make sure it is well written to avoid memory and performance issues (for example adding onClick events, interfaces, etc.)

The last thing you have to take care about is the [**getItemCount()**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter#getItemCount%28%29)method. This one will be called several times to know the size limit of the list. It generally returns size of the dataset that is used in the adapter.

## ItemDecoration

An [**ItemDecoration **](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html)allows the application to add a special drawing and layout offset to specific item views from the adapter’s data set. This can be useful for drawing dividers between items, highlights, visual grouping boundaries and more.

All [**ItemDecorations **](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html)are drawn in the order they were added, before the item views (in `[**onDraw()**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ItemDecoration#onDraw%28android.graphics.Canvas,%20androidx.recyclerview.widget.RecyclerView,%20androidx.recyclerview.widget.RecyclerView.State%29)` and after the items (in `[**onDrawOver(Canvas, RecyclerView, RecyclerView.State)**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ItemDecoration#onDrawOver%28android.graphics.Canvas,%20androidx.recyclerview.widget.RecyclerView,%20androidx.recyclerview.widget.RecyclerView.State%29)`.

For example, if you want to add divider between [**RecyclerView **](https://developer.android.com/jetpack/androidx/releases/recyclerview)items, you can use [**ItemDecoration **](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html)to achieve this – [**DividerItemDecoration**](https://developer.android.com/reference/android/support/v7/widget/DividerItemDecoration.html)to be specific.

[**ListView**](https://developer.android.com/reference/android/widget/ListView)does not support [**ItemDecoration**](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html), so you have to figure it out on your own.

## ItemAnimator

This class defines the animations that take place on items as changes are made to the adapter. Subclasses of **ItemAnimator **can be used to implement custom animations for actions on **ViewHolder **items. The **RecyclerView **will manage retaining these items while they are being animated, but implementors must call `[dispatchAnimationFinished(ViewHolder)](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ItemAnimator#dispatchAnimationFinished%28androidx.recyclerview.widget.RecyclerView.ViewHolder%29)` when a ViewHolder’s animation is finished. In other words, there must be a matching `[dispatchAnimationFinished(ViewHolder)](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ItemAnimator#dispatchAnimationFinished%28androidx.recyclerview.widget.RecyclerView.ViewHolder%29)` call for each `[animateAppearance()](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ItemAnimator#animateAppearance%28androidx.recyclerview.widget.RecyclerView.ViewHolder,%20androidx.recyclerview.widget.RecyclerView.ItemAnimator.ItemHolderInfo,%20androidx.recyclerview.widget.RecyclerView.ItemAnimator.ItemHolderInfo%29)`, `[animateChange()](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ItemAnimator#animateChange%28androidx.recyclerview.widget.RecyclerView.ViewHolder,%20androidx.recyclerview.widget.RecyclerView.ViewHolder,%20androidx.recyclerview.widget.RecyclerView.ItemAnimator.ItemHolderInfo,%20androidx.recyclerview.widget.RecyclerView.ItemAnimator.ItemHolderInfo%29)``[animatePersistence()](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ItemAnimator#animatePersistence%28androidx.recyclerview.widget.RecyclerView.ViewHolder,%20androidx.recyclerview.widget.RecyclerView.ItemAnimator.ItemHolderInfo,%20androidx.recyclerview.widget.RecyclerView.ItemAnimator.ItemHolderInfo%29)`, and `[animateDisappearance()](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.ItemAnimator#animateDisappearance%28androidx.recyclerview.widget.RecyclerView.ViewHolder,%20androidx.recyclerview.widget.RecyclerView.ItemAnimator.ItemHolderInfo,%20androidx.recyclerview.widget.RecyclerView.ItemAnimator.ItemHolderInfo%29)` call.

By default, RecyclerView uses `[DefaultItemAnimator](https://developer.android.com/reference/androidx/recyclerview/widget/DefaultItemAnimator)`.

[**ListView**](https://developer.android.com/reference/android/widget/ListView) does not support **ItemAnimator**, so in order to add some animations to it you have to create custom animations in *“anim”* folder and manually attach it to a specific views.

### Notifying Adapter

[**ListView**](https://developer.android.com/reference/android/widget/ListView) has one method that you can call in order to tell the adapter (which extends [**BaseAdapter**](https://developer.android.com/reference/android/widget/BaseAdapter)) that some of the items changed. This is [**notifyDataSetChanged()**](https://developer.android.com/reference/android/widget/BaseAdapter#notifyDataSetChanged%28%29). As you can see, this is not very much for us developers. Shouldn’t there be more than just that?

YUP!

That’s why [**RecyclerView.Adapter**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter) now includes a lot of them, so you can have full control of your items. For example [notifyDataSetChanged](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter#notifyDataSetChanged%28%29)(), [notifyItemChanged(int position)](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter#notifyItemChanged%28int,%20java.lang.Object%29), [notifyItemInserted(int position)](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter#notifyItemInserted%28int%29) any many more.. Be sure to test it out in different scenarios to fully understand it.

## Extras

### DiffUtil.Callback

[**DiffUtil**](https://developer.android.com/reference/androidx/recyclerview/widget/DiffUtil) is a utility class that calculates the difference between two lists and outputs a list of update operations that converts the first list into the second one.

It can be used to calculate updates for a RecyclerView Adapter. See `[ListAdapter](https://developer.android.com/reference/androidx/recyclerview/widget/ListAdapter)` and `[AsyncListDiffer](https://developer.android.com/reference/androidx/recyclerview/widget/AsyncListDiffer)` which can simplify the use of DiffUtil on a background thread.

DiffUtil uses Eugene W. Myers’s difference algorithm to calculate the minimal number of updates to convert one list into another. Myers’s algorithm does not handle items that are moved so DiffUtil runs a second pass on the result to detect items that were moved.

The callback helps us to skip manual handling of notifying an adapter. Instead of using different methods for each type of notifications as `notifyItemInserted()`, `notifyItemRemoved()` or `notifyItemChanged()` we just can use this simple method:

---

NOTE

[**DiffUtil**](https://developer.android.com/reference/androidx/recyclerview/widget/DiffUtil), `[**ListAdapter**](https://developer.android.com/reference/androidx/recyclerview/widget/ListAdapter)`, and `[**AsyncListDiffer**](https://developer.android.com/reference/androidx/recyclerview/widget/AsyncListDiffer)`require the list to not mutate while in use. This generally means that both the lists themselves and their elements (or at least, the properties of elements used in diffing) should not be modified directly. Instead, new lists should be provided any time content changes. It’s common for lists passed to **DiffUtil **to share elements that have not mutated, so it is not strictly required to reload all data to use **DiffUtil**.

---

#### ListAdapter

`[RecyclerView.Adapter](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter)` base class for presenting List data in a `[RecyclerView](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView)`, including computing diffs between Lists on a background thread.

This class is a convenience wrapper around `[AsyncListDiffer](https://developer.android.com/reference/androidx/recyclerview/widget/AsyncListDiffer)` that implements Adapter common default behavior for item access and counting.

While using a LiveData<List> is an easy way to provide data to the adapter, it isn’t required — you can use `[submitList(List)](https://developer.android.com/reference/androidx/recyclerview/widget/ListAdapter#submitList%28java.util.List%3CT%3E%29)` when new lists are available.

Here’s a quick example:

As you can, we just need to define [**onCreateViewHolder**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter#onCreateViewHolder%28android.view.ViewGroup,%20int%29), [**onBindViewHolder**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter#onBindViewHolder%28VH,%20int,%20java.util.List%3Cjava.lang.Object%3E%29) and [**DiffUtil.ItemCallback**](https://developer.android.com/reference/kotlin/androidx/recyclerview/widget/DiffUtil.ItemCallback) (which is the simplest type of [**DiffUtil.Callback**](https://developer.android.com/reference/kotlin/androidx/recyclerview/widget/DiffUtil.Callback)) methods. From now on it is enough to use [**adapter.submitList(newItems)**](https://developer.android.com/reference/androidx/recyclerview/widget/ListAdapter#submitList%28java.util.List%3CT%3E%29) method of [**ListAdapter**](https://developer.android.com/reference/androidx/recyclerview/widget/ListAdapter) to update the data

## Summary

[**ListView**](https://developer.android.com/reference/android/widget/ListView) has been with us for a long time, but as you can see its deputy [**RecyclerView**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView)is beyond [**ListView’s**](https://developer.android.com/reference/android/widget/ListView) capacity. It is more efficient by default, has simpler animations and adapter actions and overall using this new API is quite nice. So if you still wonder what should you choose for your lists in future apps — go for [**RecyclerView**](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView). I hope I helped with understanding the differences between these two. Be sure to test them out on your own and see how different mechanisms work
