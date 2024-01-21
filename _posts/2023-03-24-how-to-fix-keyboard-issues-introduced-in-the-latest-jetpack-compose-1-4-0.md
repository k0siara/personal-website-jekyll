---
title: 'How to fix keyboard issues introduced in the latest Jetpack Compose (1.4.0)'
date: 2023-03-31
permalink: /blog/how-to-fix-keyboard-issues-introduced-in-the-latest-jetpack-compose-1-4-0/
tags:
  - cool posts
  - category1
  - category2
---

Recently (March 22, 2023) Google released a new Jetpack Compose ****stable**** version 1.4.0. Our team got excited and wanted to try it out, so we found some time to migrate a few days later. At first, everything was working fine. It seemed like we could just bump the version and prepare the PR for review. A few tests later we found out that the keyboard is not working at all in Dialog Fragments with ComposeView, so basically 20% of our app was broken 😅 (all DialogFragments and BottomSheetDialogFragments).

Here is an example of the problem 👇

💡

We have a full-screen DialogFragment with ComposeView. Inside it, there’s only a top bar and a text field. The keyboard should appear after clicking on the text field, but unfortunately, it’s not working.

Let’s have a look a the code 👨‍💻

We start with a simple DialogFragment. In my case, I used a custom style to make it a full-screen dialog. In `onCreateView` new ComposeView is returned with Composable content. We will discuss both `dialogFragmentComposeView(...)` and `DialogContent()` in the next steps:

Next, I declared a dialog content Composable with Scaffold and one text field to make things easy to see and understand:

Last, but not least is the `dialogFragmentComposeView(...)` extension that is used to quickly create a ComposeView to be used inside a DialogFragment. Our setup only needs a few things:

- The layout params should be set to MATCH_PARENT, just to be sure that our dialog will be full-screen,
- The view composition strategy should be set to DisposeOnDetachedFromWindow. We’ve tested others multiple times and always got crashes in Jetpack Compose < 1.4.0 when working with dialogs,
- Make sure we use our theme 👨‍🎨

This is the final result of this DialogFragment and how it behaves with the latest stable Compose version (1.4.0). You can see that the TextField gains focus when you click on it, but the keyboard is not showing at all. Well, in that situation we could at least type using an external keyboard 😅
![](__GHOST_URL__/content/images/2023/03/Screen-Recording-2023-03-29-at-08.59.51-2.gif)
# Investigation 🕵️

So what is the cause of this issue? Well, we spent several hours looking for the cause of this it. We’ve tried on multiple devices, changed handling inset logic, tested on different text inputs, etc. but nothing seemed to make any difference in behavior. I started looking at the Google Issue Tracker and found a very helpful regression bug report: [https://issuetracker.google.com/issues/262140644](https://issuetracker.google.com/issues/262140644)

As it turns out, the culprit of all the confusion was a change in the InputMethodManager, [this line to be exact](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-main:compose/ui/ui/src/androidMain/kotlin/androidx/compose/ui/text/input/InputMethodManager.kt;l=159;drc=16ad8c080ac1284a39ca331e4a6169646b82e62a):

    // TODO(b/221889664) Replace with composition local when available.
    private fun View.findWindow(): Window? =
        (parent as? DialogWindowProvider)?.window
            ?: context.findWindow()

The sad thing about this huge regression is that it was reported on Dec 12, 2022, when someone was testing Jetpack Compose version 1.4.0-alpha03, more than 3 months before the STABLE release! Is Google testing new releases at all? They had plenty of time to fix it or at least keep the same behavior as in the previous versions. It seems to me like we’re always getting new Experimental APIs, but not so many bug fixes and regression fixes in the last Compose stable releases. Not cool.

# Fix it yourself👨‍🔧

I don’t know how long it will take to fix this bug, but I was curious how I can fix, or should I say hack this error myself. Fortunately, if you read the entire content of the conversation under the reported issue you can find a lot of useful information!

Because the `View.findWindow()` function checks if the parent is a `DialogWindowProvider` we could create our own version of `ComposeView` that implements not only `AbstractComposeView` but also the `DialogWindowProvider` and override the function that returns the window to get it from the `DialogFragment`. You can see below in the `DialogFragmentComposeView` I am passing the dialog in a lambda to ensure what is used any time the window is called. The rest of the code is just copy-pasted from the original `ComposeView` class.

Bear in mind you cannot just pass the `dialog` from `DialogFragment` directly to your custom `ComposeView` as it would lead to crashes. Simply put, the dialog is created and then cleared inside `onDestroyView`. On top of that Android has a messed up lifecycle, so it is possible that the call for a `window` would happen after the dialog is destroyed. I’ve tested this case several times and got crashes because of this. That is why to make it “not crashing”, you can hack it by providing `dialog ?: Dialog(requireContext())` so in that case “some” dialog always exists. Remember, this is a hack, not a perfect solution, unfortunately.

And so, after all this struggle we can finally use the keyboard in our app again. Problem solved, so we can update the app to use Compose 1.4.0, right? Well… 😅
![](__GHOST_URL__/content/images/2023/03/Screen-Recording-2023-03-29-at-16.39.47.gif)
# Did we update to Compose 1.4.0?

No. The bug presented in this article is not the only disadvantage of this “stable” version. Have a look at the release notes of the compose-ui 1.4.0 👇
[https://developer.android.com/jetpack/androidx/releases/compose-ui#1.4.0](https://developer.android.com/jetpack/androidx/releases/compose-ui#1.4.0)

One thing in the ****important changes since 1.3.0**** section is “The focus system is rewritten using the new experimental `Modifier.Node` APIs”. Based on my observations at times, the focus behavior can appear to be buggy and unpredictable, which can be frustrating for users.

Ultimately, the decision to upgrade to anything should be made based on a comprehensive assessment of the risks and benefits, with the goal of providing the best possible experience for your app’s users. We’re skipping this version and waiting patiently for the next stable one, but this time ****stable****.

# Final Thoughts

Hope I helped you overcome the issue that I also encountered myself. As previously mentioned, it is important to note that Compose 1.4.0 may have some bugs, and thus it may not be suitable for use in a production environment. However, if the issue discussed in this article is the only concern holding you back from updating, then maybe it’s worth giving it a try.

If you have experienced any other issues with Compose that are affecting your app, please feel free to share your thoughts in the comments section. I would be interested in hearing about your experiences. Thank you for reading 👋

# Before you go:

****✉️ Android Dev Newsletter****
If you enjoy learning about Android as I do and want to stay up to date with the latest, worth-reading articles, programming news, and much more, consider subscribing to my newsletter.

[https://androiddevnews.com](https://androiddevnews.com/)

****🎙 Android Talks Podcast****
If you’re a Polish speaker and want to listen to what I have to say about Android, architecture, security, and other topics, check out my podcast.

[https://androidtalks.com](https://androidtalks.com/)