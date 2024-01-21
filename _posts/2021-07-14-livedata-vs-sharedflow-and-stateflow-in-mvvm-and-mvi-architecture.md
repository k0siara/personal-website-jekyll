---
title: 'LiveData vs SharedFlow and StateFlow in MVVM and MVI Architecture'
date: 2021-07-14
permalink: /blog/livedata-vs-sharedflow-and-stateflow-in-mvvm-and-mvi-architecture/
tags:
  - cool posts
  - category1
  - category2
---

## Intro

Last year [**kotlinx.coroutines**](https://kotlin.github.io/kotlinx.coroutines/) library introduced two new [**Flow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/index.html) types, [**SharedFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-shared-flow/index.html) and [**StateFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/index.html), which also have their mutable types — [**MutableSharedFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-shared-flow/index.html) and [**MutableStateFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-state-flow/index.html).

Android community started wondering… Which one should I use now? [**LiveData**](https://developer.android.com/reference/androidx/lifecycle/LiveData) or the new types? Is [**LiveData**](https://developer.android.com/reference/androidx/lifecycle/LiveData) deprecated now?

Let’s answer all of the questions.

## ******One last thing before we start******

### ✉️ Android Dev Newsletter

If you enjoy learning about Android like I do and want to stay up to date with the latest, worth reading articles, programming news and much more, consider subscribing to my newsletter👇
[https://androiddevnews.com](https://androiddevnews.com/)

### 🎙 Android Talks Podcast

If you’re a Polish speaker and want to listen to what I have to say about Android, architecture, security and other interesting topics, check out my podcast👇
[https://androidtalks.buzzsprout.com](https://androidtalks.buzzsprout.com/)

I also recored an episode regarding this article, you can find it here:

## LiveData

Most of you should already know [**LiveData**](https://developer.android.com/reference/androidx/lifecycle/LiveData) and how it works. [**LiveData**](https://developer.android.com/reference/androidx/lifecycle/LiveData) is a data holder class that can be observed within a given lifecycle.

Example:
You create a [**LiveData**](https://developer.android.com/reference/androidx/lifecycle/LiveData) object in a [**ViewModel**](https://developer.android.com/reference/androidx/lifecycle/ViewModel) class to hold some **ViewState** and observe it in the Fragment to update UI when **ViewState** changes.

## LiveEvent

OK, how about some single operations. For example, how can the [**ViewModel**](https://developer.android.com/reference/androidx/lifecycle/ViewModel) tell [**Fragment **](https://developer.android.com/reference/androidx/fragment/app/Fragment)to show a [**Snackbar**](https://developer.android.com/reference/com/google/android/material/snackbar/Snackbar)?

By using the [**LiveEvent**](https://github.com/hadilq/LiveEvent) (or **SingleLiveEvent**), a modified [**LiveData**](https://developer.android.com/reference/androidx/lifecycle/LiveData) to handle single events, which means it emits the data just once, not after configuration changes again. The same solution can be used to display a toast, dialog, navigate to other [**Fragment**](https://developer.android.com/reference/androidx/fragment/app/Fragment), etc.

## So what is exactly the problem with [LiveData](https://developer.android.com/reference/androidx/lifecycle/LiveData) and [LiveEvents](https://github.com/hadilq/LiveEvent)?

#### LiveData is an Android library

As you know, [**LiveData**](https://developer.android.com/reference/androidx/lifecycle/LiveData) is a part of [**Jetpack**](https://developer.android.com/jetpack) and it is an Android library. It is has to be handled in Android classes and with their lifecycle. It is closely bound to the UI, so there is no natural way to offload some work to worker threads.

In Clean Architecture terms, [**LiveData**](https://developer.android.com/reference/androidx/lifecycle/LiveData) is **OK **to be used in **Presentation Layer**, but it is unsuitable for other layers, like Domain for example, which should be platform-independent (only Java/Kotlin module), Network Layer, Data Layer, etc.
![](__GHOST_URL__/content/images/max/800/1-U3sRJnuuqejQczuYxILfpw.png)[https://medium.com/](https://medium.com/)
## LiveData is OK for MVVM, but not so much for MVI

**MVI **stands for **Model**–**View**–**Intent **and it’s a design pattern that uses **Unidirectional Data Flow** to achieve something like we already have in **Flux **or **Redux**, etc.
![](__GHOST_URL__/content/images/max/800/1-dp-AOa7xWrQsiMs_noX4Cw.png)[https://medium.com/](https://medium.com/)
As you can see, the picture above shows the desired Data Flow that should be used in **MVI**. View communicates with the **ViewModel **by triggering events which are then handled inside the [**ViewModel’s**](https://developer.android.com/reference/androidx/lifecycle/ViewModel) logic, UseCases, etc. At the end, the new **ViewState **is emitted and **UI **is updated.

Handling view states using [**LiveData**](https://developer.android.com/reference/androidx/lifecycle/LiveData) is pretty easy and can be used both for **MVVM **and **MVI**, but the problem begins when we want to show a simple [**Snackbar**](https://developer.android.com/reference/com/google/android/material/snackbar/Snackbar) like before. If we use the [**LiveEvent**](https://github.com/hadilq/LiveEvent) class for that then the whole Unidirectional State Flow is disturbed, since we just triggered an event in [**ViewModel**](https://developer.android.com/reference/androidx/lifecycle/ViewModel) to interact with **UI**, but it should be the opposite way.

So, now we’ve just created a combination of **MVVM **and **MVI **and we confuse a lot of people on what is an event exactly and how the architecture works. Not cool, right?

To get it right in **MVI** you should treat these single “events” as side effects. You can use [**Channels**](https://kotlinlang.org/docs/channels.html)for that, but this is a topic for another article.

BTW. Don’t worry, [**LiveData**](https://developer.android.com/reference/androidx/lifecycle/LiveData) is not going to be deprecated. You can still use it if you like it :)

## OK, so what now? We have SharedFlow, StateFlow, but we had Flow already in Kotlin before. Can’t we use it?

**Unfortunately no.**

1. [**Flow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/index.html) is stateless, it has no *.value* property. It is just a stream of data that can be collected.
2. [**Flow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/index.html) is declarative (cold). It is only materialized when collected and for each new collector there will be a new [**Flow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/index.html) created. This is not a good option for doing expensive stuff, like accessing the database and other things that don’t have to be repeated every time.
3. [**Flow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/index.html) has no idea about Android and lifecycles. It doesn’t provide automatic starting, pausing, resuming of collectors upon Android lifecycle state changes.

---

**BUT WAIT, (3) is not so true now…**

This was solved by adding an extension method [**launchWhenStarted**](https://developer.android.com/reference/kotlin/androidx/lifecycle/LifecycleCoroutineScope#launchWhenStarted%28kotlin.coroutines.SuspendFunction1%29)to [**LifecycleCoroutineScope**](https://developer.android.com/reference/kotlin/androidx/lifecycle/LifecycleCoroutineScope), but I see most people don’t know how to use it properly. It is simply not enough, since [**Flow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/index.html) has a *subscription count* property that won’t be changed when **Lifecycle.Event** reaches [**ON_STOP**](https://developer.android.com/reference/kotlin/androidx/lifecycle/Lifecycle.Event#on_stop). This means that the [**Flow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/index.html) will be still active in memory and could cause memory leaks!

---

To solve this problem you can create a custom observer that will launch collect method when [**ON_START**](https://developer.android.com/reference/kotlin/androidx/lifecycle/Lifecycle.Event#on_start) event is triggered and cancel its job on [**ON_STOP**](https://developer.android.com/reference/kotlin/androidx/lifecycle/Lifecycle.Event#on_stop). There are also other methods that you can use to achieve the same result, like [**repeatOnLifecycle**](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary?hl=id#%28androidx.lifecycle.Lifecycle%29.repeatOnLifecycle%28androidx.lifecycle.Lifecycle.State,%20kotlin.coroutines.SuspendFunction1%29) or [**flowWithLifecycle**](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary?hl=id#flowwithlifecycle).

For example:

Then we can use it like this:

## SharedFlow and StateFlow to the rescue!

### Let’s talk about SharedFlow first

**SharedFlow** is a type of **Flow** that shares itself between multiple collectors, so it is only materialized once for every subscriber. What else it can do?

1. [**SharedFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-shared-flow/index.html) in contrast to a normal [**Flow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/index.html) is hot, every collector uses the same [**SharedFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-shared-flow/index.html), because it is **shared**.
2. [**SharedFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-shared-flow/index.html) has its buffer called *replay cache*. It keeps a specific number of the most recent values in it. Every new subscriber gets the values from the replay cache and then gets new emitted values. You can set the maximum size of the replay cache in *replay* parameter in the constructor. A replay cache also provides buffer for emissions to the shared flow, allowing slow subscribers to get values from the buffer without suspending emitters. A [**SharedFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-shared-flow/index.html) with a buffer can be configured to avoid suspension of emitters on buffer overflow using the **onBufferOverflow **parameter, which is equal to one of the entries of the [**BufferOverflow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.channels/-buffer-overflow/index.html) enum. When a strategy other than **SUSPENDED **is configured, emissions to the shared flow never suspend.
3. If you use the default [**SharedFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-shared-flow/index.html) constructor of [**MutableSharedFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-shared-flow/index.html) then the *replay cache* won’t be created.

You can also transform a normal [**Flow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/index.html) into a [**SharedFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-shared-flow/index.html) using this extension method:fun <T> Flow<T>.shareIn(
  scope: CoroutineScope, 
  started: SharingStarted, 
  replay: Int = 0
): SharedFlow<T> (source)

Let’s see how we can use **SharedFlow **to handle events. The **BaseViewModel **and **BaseFragment **would look something like this:

And then in **ViewModel **you can handle triggered events:

**onAddAddressClicked()** and **onRemoveAddressClicked(address: Address)** are used in **DataBinding**, so we have to trigger events here. Can you see how simple this is? Clean, readable code that can be easily tested (tests included in the **Github Repository** at the end of the article)

### What about StateFlow?

[**StateFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/) is a [**SharedFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-shared-flow/index.html) with a couple other things:

1. When creating a [**StateFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/) you have to provide its *initialState*.
2. You can access [**StateFlow’s**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/) current state by *.value* property, just like in [**LiveData**](https://developer.android.com/topic/libraries/architecture/livedata).
3. If you add a new collector in the meantime then it will automatically receive current state. Also, it won’t get any info about previous states, but only the new ones that will be emitted.

You can also transform a normal [**Flow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/index.html) into a [**StateFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/) using this extension method:fun <T> Flow<T>.stateIn(     
  scope: CoroutineScope,      
  started: SharingStarted,      
  initialValue: T 
): StateFlow<T> (source)

Let’s see how we can use [**StateFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/) and [**SharedFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-shared-flow/) together to update state after event is handled. The **BaseViewModel **and **BaseFragment **would look something like this:

And then in **ViewModel **you can update state like this:

That’s it! Simple, right?

## Example Github Repository (Jetpack Compose, MVI)

I’ve created a **Github Repository **with examples on standard **Fragments **and **Jetpack Compose** with [**SharedFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-shared-flow/index.html) and [**StateFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/). There are also unit tests and UI/Screenshot tests included for you to see how easy it is to test MVI.

You can find it here: [https://github.com/k0siara/AndroidMVIExample](https://github.com/k0siara/AndroidMVIExample)
