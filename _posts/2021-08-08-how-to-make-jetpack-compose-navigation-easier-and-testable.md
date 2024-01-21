---
title: 'How to make Jetpack Compose navigation easier and testable'
date: 2021-08-08
permalink: /blog/how-to-make-jetpack-compose-navigation-easier-and-testable/
tags:
  - cool posts
  - category1
  - category2
---

## Navigating in Compose is easy

Navigating in Jetpack Compose is pretty simple as you may already know. You just declare a `NavHost` with all your destinations and a `NavHostController` that will remember the navigation state and will let you controll your navigation.

For example:

We’ve just created a `navController` using `rememberNavController()` so that navigation state can survive recompositions and we declared two destinations: `"firstScreen”` and `"secondScreen"` so we can easily navigate between them.

Now to navigte from **FirstScreen** to **SecondScreen **we just use something like this:

Simple, right? But it has some drawbacks…

## **************One last thing before we start**************

### ✉️ Android Dev Newsletter

If you enjoy learning about Android like I do and want to stay up to date with the latest, worth reading articles, programming news and much more, consider subscribing to my newsletter👇
[https://androiddevnews.com](https://androiddevnews.com/)

### 🎙 Android Talks Podcast

If you’re a Polish speaker and want to listen to what I have to say about Android, architecture, security and other interesting topics, check out my podcast👇
[https://androidtalks.buzzsprout.com](https://androidtalks.buzzsprout.com/)

## The problem

While the idea is pretty simple and navigating between composables is easy, there are some problems with it:

- Everytime you need to navigate you’ll need the `navController` reference. This means that if you have a really complex screen and a lot of composables down the widget tree then every composable down the road will need a `navController` as function parameter.
- It’s hard to test. Navigation inside composables can be tested by writing instrumentation tests, but why can’t we just unit test it to make our life easier and to run tests faster?
- Let’s say we want to intercept every navigation. For example, we want to save it somewhere or check/do something before or after the navigation happens. Can we do that? Well, we could create a custom NavHost or a custom composable, but still you wouldn’t be able to unit test it.
- What if we want to do some logic in the `ViewModel` and then based on the result navigate to different screens? This would require a lot of boilerplate code in every composable that you want to navigate to.

## What can we do about it?

I’ve been thinking a lot about it and I’d like to share my idea on how we could fix this problem. This solution will allow us to trigger navigation from the `ViewModel` and to easily unit test it.

---

### 1. Define your destinations and navigation actions

First thing we have to do is to define all destinations and navigation actions between them.

In this case we will have 3 destinations for user to navigate to, where `"firstScreen"` destination is going to be our `startDestination`. To create a `NavigationAction` you just have to override the`destination` param. It is also possible to pass params between screens, like in `secondScreenToThirdScreen` example.

### 2. Create a custom navigator

The second step is to create our custom navigator that will hold the current navigation state. Let’s see an example:

Initially there will be no action, so that’s why there is a `null` value passed to the MutableStateFlow.

### 3. Add navigator to your Dependency Injection Framework

We want to be able to use the `Navigator`in our `ViewModels.` The easiest way to make it work is to just create a singleton in our Dependency Injection Framework. From now on we can simply inject it to our `ViewModels.` In this example I am using Koin:

### 4. Adjust your navigation graph

Now, our `NavHostExample` composable will get a `Navigator` injected and will be able to navigate every time the navigation state changes. As you can see there is no problem with passing and handling either named arguments or Parcelables.

You may be wondering, what is this `asLifecycleAwareState` extension method that I use here:val navigatorState by navigator.navActions.*asLifecycleAwareState*(
    lifecycleOwner = lifecycleOwner,
    initialState = null
)

This is a custom extension method that will allow us to collect the **StateFlow **only when we need to. Flow is not aware of Android lifecycle by default, so we have to make it work somehow.

This is the source code of the extension method:

### 5. Voila! Use our custom navigator to navigate from the ViewModel

Now, we can easily navigate from our `ViewModel` just by calling:

This allows us to keep all navigation logic inside `ViewModels` and makes it testable and easy to use. You can always extend the `Navigator` class to have more functions and navigation options if you want.

## Thanks for reading!

That’s it for this article. Jetpack Compose is still evolving so I hope there will be some improvements to the navigation component in the future. For now this is the solution that I use on a daily basis for my Android apps. I hope you like it and should you have any questions, do not hesitate to comment or reach out to me :)
