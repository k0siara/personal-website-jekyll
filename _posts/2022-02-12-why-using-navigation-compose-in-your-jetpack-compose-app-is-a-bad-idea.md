---
title: 'Why using Navigation-Compose in your Jetpack Compose app is a bad idea'
date: 2022-02-12
permalink: /blog/why-using-navigation-compose-in-your-jetpack-compose-app-is-a-bad-idea/
tags:
  - cool posts
  - category1
  - category2
---

## Intro

If you’re reading this article, you’re probably thinking:

*“Hmm I wonder what to use to navigate in my Jetpack Compose app. Should I use navigation-compose as sugested by Google or stick with Fragments and use Compose only for rendering views?”*

In this article, I’m going to talk about my experience with both Navigation-Compose and Fragments in a Jetpack Compose app. You will learn which approach is better (in my opinion) and what obstacles may await you by using each of them.

## **************One last thing before we start**************

### ✉️ Android Dev Newsletter

If you enjoy learning about Android like I do and want to stay up to date with the latest, worth reading articles, programming news and much more, consider subscribing to my newsletter👇
[https://androiddevnews.com](https://androiddevnews.com/)

### 🎙 Android Talks Podcast

If you’re a Polish speaker and want to listen to what I have to say about Android, architecture, security and other interesting topics, check out my podcast👇
[https://androidtalks.buzzsprout.com](https://androidtalks.buzzsprout.com/)

You can listen to an episode I’ve made about this topic:

## Let’s start

If you Google* “Jetpack Compose Navigation”* you will probably see the Navigation-Compose library as one of the first results. Google describes it as a new navigation component that supports apps written in Compose and claims that ***thanks to it we can navigate between composables while taking advantage of the Navigation component’s infrastructure and features.***

Sounds perfect, but is it? We should talk about what the new navigation looks like…

Google really wanted to create a Framework that would be able to manage lifecycles, navigation and everything around it while eliminating the need to use Fragments. They came up with the idea to create a navigation graph as Composable ([**NavHost**](https://developer.android.com/jetpack/compose/navigation#create-navhost)) instead of an XML file, where we can define routes to which the application can navigate. In fact, if you look closely, Fragments are replaced by navBackstackEntry in this way. Unfortunately, it doesn’t work very well and there are a lot of issues with it.

## Route paths

First of all — route paths.

Google probably envied Flutter, React and other similar Frameworks and thought that the best solution for Android would be to define screen routes as in a web app — despite the fact that we create mobile applications, not web ones, right?

What does this mean for us?

This means that each route looks like an URL ending, e.g.

- **/users **— for the user list screen
- **/users/2** — for the detail screen for user with id = 2.

From now on, the developer must maintain the order of the paths and their parts, remember them and their params, or create constants for individual screens, which already means that we have a lot more work to start than before.

What comes next is even more confusing.

## Navigation arguments

Earlier, using Fragments and Safe Args, we could define in XML what arguments a given Fragment takes. When creating an action from one screen to another, we had an automatically generated code that asked us for arguments of the correct type and packed them into the **Bundle**. Next, to extract these arguments in the target Fragment, we can use the **by navArgs() **delegate for example, which will do it for us automatically.

We were able to pass a lot of different types as parameters, such as Int, String, Boolean, etc. but also custom ones, such as Enums, Serializables and Parcelables. The full list of supported types can be found [**here**](https://developer.android.com/guide/navigation/navigation-pass-data#supported_argument_types).

Is it just as convenient in navigation compose? **NOT AT ALL**

Arguments can be passed only in the URL path of a given route, for example:

- Path params
***/users/{arg1}/details/{arg2}***
- Or to make it even more complicated, you can also pass query params and optional parameters after the question mark
***/users/{arg1}/details?{arg2}={arg2_value}&{arg3}={arg3_value}***

Oh, and don’t forget that for each destination and param that you want to use, **YOU **are responsible for all of the type safety. Compose navigation is not going to tell you that you mistook an Int param as a String. It will just crash the app.

Each param has to be declared in **composable **in the **NavHost:**

And also extracted with a proper type later:

So now, not only that the programmer has to manage all routes himself, he also has to remember what arguments are taken by a particular screen and what are the keys and data types of parameters and their order.

Will you get the errors at compile time that you messed something up? Nope. You will get an error or an app crash only while the app is running.

## Everything is a String

Also, don’t forget that since the screen route is like an url, each parameter must be passed anyway as a String and **must be encoded**.

Let’s say we want to pass this String as a param:val urlParam = "[https://translate.google.com/?hl=en&tab=TT](https://translate.google.com/?hl=en&amp;tab=TT)"

To navigate with it we have to write this:navController.navigate("path/arg=${URLEncoder.encode(urlParam)}")

And then receive it like this:URLEncoder.decode(bundle.getString("arg_key"))

Even better, if you use **URLEncoder.encode(…)** over a String that contains a **‘\n’** character, it will crash because of the **‘%0A’**, so the only way to make it work is to use **Base64** encoding first.

This makes the Navigation-Compose API completely NON type safe.

### What about Enums, Serializables and Parcelables?

Like I’ve mentioned before, any argument we want to pass, regardless of type, has to be converted to a String in order to add it to the path.

#### Enums

Compose navigation won’t let you pass **Enums **as params, but this can be done differently. In theory, we can convert it into a String, and call the **valueOf** method on the target screen to find the value in the enum that we previously passed as String.

## Parcelables and Serializables

This is where the fun begins. Passing **Serializable** and **Parcelable** as a navigation param in Compose-Navigation is not supported too.

I see a lot of people on the Internet saying that it is possible to get into the **backStackEntry **arguments and **manually **pass the object there that extends **Serializable **or **Parcelable**.

**HOWEVER**
This is just a hack that Google does not recommend and does not give any guarantee that this will work.

Personally, I have had a lot of situations where passing arguments in this way caused the app to crash.

Don’t believe me? Just go here and read:
[**https://issuetracker.google.com/issues/182194894**](https://issuetracker.google.com/issues/182194894)

## Is there anything we can do?

Well, theoretically… yeah. We could convert our **Serializable** or **Parcelable **object to **JSON** and pass it as a String. I feel sorry for anyone who would like to pass params this way. This is a way to do it, but it’s so stupid and brings so much unnecessary boilerplate and complexity that I won’t even consider using it.

#### **UPDATE!**

Since **Compose **version **1.0.3** and **NavigationX **`**2.4.0-alpha10**`we are now able to create a custom **NavType**:

Let’s say you have a class like this:

You can define the **NavType **like this:

And use it:

This seems better than what we had before **BUT **still requires us to use **JSON **inside. Also imagine creating a new custom **NavType** every time you want to pass a Parcelable. This just adds more boilerplate code to our app. Moreover there is **no mention** in the documentation from Google that this solution will work with Compose-Navigation too.

## But WHY?

And now the main question arises — why?

Why is Navigation-Compose like that? Why doesn’t it support Serializables, Parcelables and why does it have so many issues?

It is very possible that Google wants to encourage developers to pass only id of the object and not the static copy of it. It would make sense if, for example, we were operating on the Room database and observing the data using Flow. For example, if something in the meantime changed this data, then the user would automatically see the current values on the screen.

Indeed, maybe in a few cases like this, it would make sense. Unfortunately, there are often situations in which we want to consciously pass a static object, or at least some part of it so that the user immediately sees the data on the screen. After that, we could load some details in the background or simply check if the data is up-to-date.

## Just use Fragments

Okay, then how should I use Compose to avoid these problems? The answer is simple. Use Fragments and old navigation.

#### **Reasons why you should still use Fragments**

You can use whatever you want: **XML**, **Compose**, you choose. The easiest way is to add **ComposeView **to the **Fragment** view and set **Composable **of the given **Fragment** there.

Not a fan of LaunchedEffect, DisposableEffect and so on? You don’t have to use them as you can write some code the old way, just like it was in Fragments.

Depending on what **DI Framework** you are using, you may also run into some issues with **SavedStateHandle **if you use Navigation-Compose. 
In my current project, we are using **Koin** and unfortunately **SavedStateHandle **cannot be injected into the **ViewModel **in the new navigation. This problem, of course, has to be fixed by Koin Team, but for now, it disappears if you use **Fragments **and inject the ViewModel using **by viewModel()** delegate.

#### Of course, Fragments aren’t perfect. What issues with them should you consider in this case?

First of all, you are using Fragments. This may seem funny at first, but some people don’t like them, for example Jake Wharton. In short, he says you can use Fragments, but the backstack is a nightmare.

You have a little more code. After all, for each screen, you have to write both **Fragment** and **Composable**.

Another thing is that you have two lifecycles that you need to manage — **Fragment** and **Composable** lifecycle. This may seem problematic at first, but fortunately, there is a quick way to deal with it. Just use the **setViewCompositionStrategy** method on a **ComposeView** and set how the **Composable’s** lifecycle should behave in relation to the **Fragment’s** lifecycle.

Last but not least, you must remember that when passing a **Parcelable **object as a Safe Args argument, you must also add it to the proguard configuration, otherwise you will get a crash on production.

## Summary

So is using Fragments the best solution? Probably not.

In my opinion, it would be best if Google provided us with a ready, working and good API for navigation in Compose. Unfortunately, as I mentioned before, we can forget about it for now. At the moment, the solution that I presented is something that I use myself and I’m OK with it, at least for now.

We should also remember that many libraries and tools do not yet have 100% support with Compose and sometimes the ability to write something in the old way, in a **Fragment** or even in **XML** is exactly what we need.

## Thanks for reading!

That’s it for this article. I hope you like it and should you have any questions, do not hesitate to comment or reach out to me :)
