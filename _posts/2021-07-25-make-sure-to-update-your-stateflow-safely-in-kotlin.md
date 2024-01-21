---
title: 'Make sure to update your StateFlow safely in Kotlin!'
date: 2021-07-25
permalink: /blog/make-sure-to-update-your-stateflow-safely-in-kotlin/
tags:
  - cool posts
  - category1
  - category2
---

Hi, today I come to you with a quick tip on how to update your **StateFlows **safely in Kotlin.

Recently a new version of **Kotlin Coroutines **library was released with a few new extensions functions to help you with **StateFlow **updates. It all started with this issue:
[

Expose atomic updates on MutableStateFlow · Issue #2720 · Kotlin/kotlinx.coroutines

qwwdfsad added a commit that referenced this issue May 25, 2021&nbsp;...ableStateFlow (#2729) * Add update, updateAndGet…

github.com

![](https://cdn-images-1.medium.com/fit/c/160/160/0*q0_4S-Cp33p0BjXf)
](https://github.com/Kotlin/kotlinx.coroutines/issues/2720)
Let’s see what it’s all about…

## **************One last thing before we start**************

### ✉️ Android Dev Newsletter

If you enjoy learning about Android like I do and want to stay up to date with the latest, worth reading articles, programming news and much more, consider subscribing to my newsletter👇
[https://androiddevnews.com](https://androiddevnews.com/)

### 🎙 Android Talks Podcast

If you’re a Polish speaker and want to listen to what I have to say about Android, architecture, security and other interesting topics, check out my podcast👇
[https://androidtalks.buzzsprout.com](https://androidtalks.buzzsprout.com/)

## First time using StateFlow?

If it’s your first time with **StateFlow **and you want to know more about it and how it relates to others like **SharedFlow **or **LiveData**, you can read my article about it here:
[

LiveData vs SharedFlow and StateFlow in MVVM and MVI Architecture

See the original article on my website…

proandroiddev.com

![](https://cdn-images-1.medium.com/fit/c/160/160/1*xBCWY4rKovjRmP7C2McVCA.jpeg)
](https://proandroiddev.com/livedata-vs-sharedflow-and-stateflow-in-mvvm-and-mvi-architecture-57aad108816d)
## Let’s begin

**StateFlow **is mostly used for handling state and state updates. It can be used for example in Android’s **ViewModel **to expose state to your views.

Let’s see an example:

How to collect this state now in **Fragment **for example? All info can be found in my article I mentioned before :)

### How can we update the state?

Well, it’s really easy, since we use a data class. We can just use **copy **like this:

    _uiState.value = _uiState.value.copy(title = "Something")
    

Simple, right? But you have to be careful…

## The issue

So what’s the problem? While the code is very simple, there is something you have to be aware of: **CONCURRENCY**.

If between the time **copy **function completes and the **StateFlow’s **new value is emitted another thread tries to update the **StateFlow — **by using **copy **and updating one of the properties that the current **copy **isn’t modifying —we could end up with results we were not expecting.

## Solution

How do we solve this problem? By using fresh good stuff from **Kotlin Coroutines **for **MutableStateFlow**.

The stuff I’m talking about are these three methods:

- [update](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/update.html)
- [updateAndGet](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/update-and-get.html)
- [getAndUpdate](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/get-and-update.html)

All of these take a function parameter that returns the new state that will be emitted.

Let’s see what’s inside the [update](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/update.html) method for a moment:

    public inline fun <T> MutableStateFlow<T>.update(function: (T) -> T) {
        while (true) {
            val prevValue = value
            val nextValue = function(prevValue)
            if (compareAndSet(prevValue, nextValue)) {
                return
            }    
        }
    }
    

As we can see, a function is passed as a param and it’s applied to the current **StateFlow**’s value. Then **compareAndSet **function is used to determine if the value has changed — for example by another thread. If **compareAndSet **returns false then the while loop will be running until it’s possible to update the state.

So how to use it? Let’s see an example **SAFE **update now:

    _uiState.update { it.copy(title = "Something") }
    

That’s it! This ensures us that the state can be changed **safely**!

---

That’s all for this article. I hope you’ll update your **StateFlow** safely now. Be sure to check out my other articles on the website!
