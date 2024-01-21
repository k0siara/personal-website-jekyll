---
title: 'Easy UI And Screenshot Testing On Android'
date: 2021-05-01
permalink: /blog/easy-ui-and-screenshot-testing-on-android/
tags:
  - cool posts
  - category1
  - category2
---

## Ways of testing on Android

Every good Android application should be well tested to minimize the risk of error after releasing it to the world. The most basic tests for any application are Unit Tests. You **must** write them to ensure that a particular part of the code is working.

On Android we have also Instrumentation Tests — tests that run on physical devices and emulators, and they can take advantage of the Android framework APIs and supporting APIs, such as AndroidX Test. One of many ways of using this mechanism is to test the UI and take screenshots of every screen in our app. I really like this, because if you run this UI/Screenshot Test every time before creating a new pull request, you can easily check if you didn’t change something in the UI by mistake.

## **One last thing before we start**

### ✉️ Android Dev Newsletter

If you enjoy learning about Android like I do and want to stay up to date with the latest, worth reading articles, programming news and much more, consider subscribing to my newsletter👇
[https://androiddevnews.com](https://androiddevnews.com/)

### 🎙 Android Talks Podcast

If you’re a Polish speaker and want to listen to what I have to say about Android, architecture, security and other interesting topics, check out my podcast👇
[https://androidtalks.buzzsprout.com](https://androidtalks.buzzsprout.com/)

## How to capture device screenshots?

I’ve worked on a few projects that used screenshot testing and these test were always dependent on external libraries like [**Karumi Shot**](https://github.com/Karumi/Shot) or [**Facebook Screenshot Tests For Android**](https://github.com/facebook/screenshot-tests-for-android). These libraries were honestly a pain in the ass, completely impossible to set up and use in a long term. One guy from my project spent a few days to manually fix some code inside the library to make it work on Windows 10. I decided that this was too much for a simple operation like taking a device screenshot, so I began to research for a better solution.

#### What did I find?

Well, first of all, **Android 4.3** is a minimum system version we need to have in our project, because we are gonna be using [**UI Automator**](https://developer.android.com/training/testing/ui-automator) under the hood. The solution I found is simply… using [**Android Support Test Library**](https://developer.android.com/studio/test). This library contains a [**Screenshot**](https://developer.android.com/reference/android/support/test/runner/screenshot/Screenshot) class and [**capture()**](https://developer.android.com/reference/android/support/test/runner/screenshot/Screenshot.html#capture%28%29) method that we’re gonna need.

**This is great, because all of the screenshots can be taken, even if, for example, the Activity/Fragment is not currently displayed on the screen or a system dialog is in the foreground.**

As I said, we’re gonna need the **Screenshot.capture()** method:

Creates a `[**ScreenCapture**](https://developer.android.com/reference/android/support/test/runner/screenshot/ScreenCapture.html)`that contains a `[**Bitmap**](https://developer.android.com/reference/android/graphics/Bitmap.html)`of the visible screen content for Build.VERSION_CODES.JELLY_BEAN_MR2 and above.

Note: Only use this method if all your tests run on API versions Build.VERSION_CODES.JELLY_BEAN_MR2 or above. If you need to take screenshots on lower API levels, you need to use `[**capture(Activity)**](https://developer.android.com/reference/android/support/test/runner/screenshot/Screenshot#capture%28android.app.Activity%29)` or `[**capture(View)**](https://developer.android.com/reference/android/support/test/runner/screenshot/Screenshot#capture%28android.view.View%29)` for those versions.

## Processing the screenshot

In order to process the screenshot, we have to use two things: [**ScreenCapture **](https://developer.android.com/reference/android/support/test/runner/screenshot/ScreenCapture.html)and [**ScreenCaptureProcessor**](https://developer.android.com/reference/android/support/test/runner/screenshot/ScreenCaptureProcessor).

There is a default implementation called [**BasicScreenCaptureProcessor**](https://developer.android.com/reference/android/support/test/runner/screenshot/BasicScreenCaptureProcessor), which does the following:

A basic `[**ScreenCaptureProcessor**](https://developer.android.com/reference/android/support/test/runner/screenshot/ScreenCaptureProcessor)`for processing a `[**ScreenCapture**](https://developer.android.com/reference/android/support/test/runner/screenshot/ScreenCapture)`.

This will perform basic processing on the given `[**ScreenCapture**](https://developer.android.com/reference/android/support/test/runner/screenshot/ScreenCapture)`such as saving to the public Pictures directory, given by android.os.Environment.getExternalStoragePublicDirectory(DIRECTORY_PICTURES), with a simple name that includes a few characteristics about the device it was saved on followed by a UUID.

**This API is currently in beta.**

### Taking a simple screenshot

At this point, if you were to take a simple screenshot, you’d just have to use a function like this:

    private const val TAG = "ScreenshotsUtils"
    
    fun takeScreenshot(screenShotName: String) {
        Log.d(TAG, "Taking screenshot of '$screenShotName'")
        val screenCapture = Screenshot.capture()
        try {
            screenCapture.apply {
                name = screenShotName
                process()
            }
            Log.d(TAG, "Screenshot taken")
        } catch (ex: IOException) {
            Log.e(TAG, "Could not take a screenshot", ex)
        }
    }

**WARNING**

Make sure you have **WRITE_EXTERNAL_STORAGE** permission added to the manifest (adding it only for debug build is fine). If you run on API 23+ (Marshmallow), you will also need to have those permissions granted before running the test.

You can “hack” this, by using **installOptions** in AGP (Android Gradle Plugin). Just put this in your **build.gradle** file:

    android {
        // The rest of you build.gradle
        
        adbOptions {
            installOptions '-g', '-r'
        }
    }

#### Custom ScreenCaptureProcessor

If you want to set a custom name for your screenshots and save to a specific location, rather than default **“screenshots”** folder under **“Picture”** on the device. Since you could use other apps on the same device, it’ll be better to set up a specific folder on the device. I wanted to make it event better, so I made an implementation that also takes into account flavors and build types:

    class MyScreenCaptureProcessor : BasicScreenCaptureProcessor() {
    
        init {
            this.mDefaultScreenshotPath = File(
                File(
                    getExternalStoragePublicDirectory(DIRECTORY_PICTURES),
                    "${BuildConfig.APPLICATION_ID}/${BuildConfig.BUILD_TYPE}"
                ).absolutePath,
                SCREENSHOTS_FOLDER_NAME
            )
        }
    
        override fun getFilename(prefix: String): String = prefix
    
        companion object {
            const val SCREENSHOTS_FOLDER_NAME = "screenshots"
        }
    }

Now, in order to take screenshots we have to update the **takeScreenshot(…)** function a little:

    private const val TAG = "ScreenshotsUtils"
    
    fun takeScreenshot(screenShotName: String) {
        Log.d(TAG, "Taking screenshot of '$screenShotName'")
        val screenCapture = Screenshot.capture()
        val processors = setOf(MyScreenCaptureProcessor())
        try {
            screenCapture.apply {
                name = screenShotName
                process(processors)
            }
            Log.d(TAG, "Screenshot taken")
        } catch (ex: IOException) {
            Log.e(TAG, "Could not take a screenshot", ex)
        }
    }

### Android Test Orchestrator

The next thing we have to do is to protect our UI tests against test crashes. Normally if you run a lot of tests and one of them crashes it will stop the rest of the remaining tests. If you want to avoid it then you have to use [**Android Test Orchestrator**](https://developer.android.com/training/testing/junit-runner) in your project.

A simple configuration for [**Android Test Orchestrator**](https://developer.android.com/training/testing/junit-runner) looks like this:

    android {
      defaultConfig {
       ...
       testInstrumentationRunnerArguments clearPackageData: 'true'
       testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
     }
    
      testOptions {
        execution 'ANDROID_TEST_ORCHESTRATOR'
      }
    }
    
    dependencies {
      androidTestImplementation 'com.android.support.test:runner:1.0.1'
      androidTestUtil 'com.android.support.test:orchestrator:1.0.1'
    }

### Turning off the animations

Another wise thing to do is to turn off the animations during tests. This setting could prevent unwanted lags and can speed up the tests. To do that, you just have to add **animationsDisabled** property to the** build.gradle**:

    android {
      testOptions {
        ...
        animationsDisabled = true
      }
    }

### Retrieve screenshots from the device

If we run our tests now then we should see screenshots saved on the device. Great! But how do we pull them from the device to a specific directory on our PC?

To solve this we have to create a few Gradle Tasks

- First one for creating screenshots directory
- Second one for fetching screenshots
- And the last one for clearing screenshots from the device after fetching

Fetching your screenshots will be done automatically, right after **connectedDebugAndroidTest** Gradle Task. To make this work, add this to your **build.gradle**:

    def appId = "com.patrykkosieradzki.moviebox"
    
    android {
        ...
    
        defaultConfig {
            ...
            applicationId appId
        }
    }
    
    def projectScreenshotsDirectory = "$projectDir/screenshots"
    def deviceScreenshotsDirectory = '/sdcard/Pictures/' + appId + '/debug/screenshots'
    
    def clearScreenshotsTask = task('clearScreenshots', type: Exec) {
        println deviceScreenshotsDirectory
        executable "${android.getAdbExe().toString()}"
        args 'shell', 'rm', '-r', deviceScreenshotsDirectory
    }
    
    def createScreenshotDirectoryTask = task('createScreenshotDirectory', type: Exec, group: 'reporting') {
        executable "${android.getAdbExe().toString()}"
        args 'shell', 'mkdir', '-p', deviceScreenshotsDirectory
    }
    
    def fetchScreenshotsTask = task('fetchScreenshots', type: Exec, group: 'reporting') {
        executable "${android.getAdbExe().toString()}"
        args 'pull', deviceScreenshotsDirectory + '/.', projectScreenshotsDirectory
        finalizedBy {
            clearScreenshotsTask
        }
    
        dependsOn {
            createScreenshotDirectoryTask
        }
    
        doFirst {
            new File(projectScreenshotsDirectory).mkdirs()
        }
    }
    
    tasks.whenTaskAdded { task ->
        if (task.name == 'connectedDebugAndroidTest') {
            task.finalizedBy {
                fetchScreenshotsTask
            }
        }
    }

Now, to run tests and fetch screenshots, just execute the following in you project’s directory:

    ./gradlew connectedDebugAndroidTest

You should see a new folder created right after, called **“screenshots”**.

If you want to learn more about ADB commands, you learn a lot here: [https://developer.android.com/studio/command-line/adb](https://developer.android.com/studio/command-line/adb)

## Show me the Github Repository

To sum it up I’ve created a simple movie app on Github that you can find here:
[

k0siara/AndroidMovieBox

Simple Android App written in Kotlin, using MVI and Clean Architecture to manage movie info from…

github.com

![](https://cdn-images-1.medium.com/fit/c/160/160/0*cAK1b1nGn49jbxDL)
](https://github.com/k0siara/AndroidMovieBox)
That’s all folks. Hope this helps you with building a good, well tested Android app. As always, if you have any questions you can either write a comment or message me directly. Bye and happy testing!
