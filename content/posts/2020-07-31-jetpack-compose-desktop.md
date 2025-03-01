---
title: Jetpack Compose - Desktop
date: "2020-07-31T00:00:00Z"
categories: [ Tools ]
tags: [ Multiplatform ]
---

*"Android’s modern toolkit for building native UI"*

[Jetpack Compose](https://developer.android.com/jetpack/compose) simplifies and accelerates UI development on Android. Quickly bring your app to life with less code, powerful tools, and intuitive Kotlin APIs.



While compose is concentrated on a UI Toolkit for Android, there was a mysterious commit in [AOSP gerrit](https://android-review.googlesource.com/c/platform/frameworks/support/+/1290729) , mentioned *"Desktop UI work - basic infra."* This creates a lot of attention among developers, since this is one the most popular feature for mobile developers, which is Multi Platform, writing the code once and run it everywhere.  [Flutter](https://flutter.dev/) was launched in May 2017, which is one of the modern framework that supports Multi Platform.



## Trying the mysterious commit

So I was interested to experiment this commit made in [AOSP gerrit](https://android-review.googlesource.com/c/platform/frameworks/support/+/1290729), which includes sample for running desktop app via Compose. Compose was developed as part of framework/base repo in the gerrit.

compose-desktop is backed up by [skia](https://skia.org/). 

> Skia is an open source 2D graphics library which provides common APIs that work across a variety of hardware and software platforms. It serves as the graphics engine for Google Chrome and Chrome OS, Android, Flutter, Mozilla Firefox and Firefox OS, and many other products.

Also, using [skija](https://github.com/JetBrains/skija) on top of skia

> A modern graphic toolkit allows you to build all sorts of graphical UIs without being constrained by existing frameworks

The problem was skija currently supports only MacOS. Since I don't have a machine running MacOS, this experiment was on hold for some time. Later on *June 27th* jetbrains released a new library [skiko](https://github.com/JetBrains/skiko), which provides KMP(KotlinMultiPlatform) support for skija.

So with a little hack (Using skiko lib instead of skija) , I was able to run the compose-desktop samples, and below is screencast of it,


<iframe width="560" height="315" src="https://www.youtube.com/embed/rsj7ItYGJYw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


> Note : Created a new Android project and copy pasted the same compose fun to run in Android.

### Setup

-  Start by setting up repository using a this [guide](https://cs.android.com/androidx/platform/frameworks/support).
- Once the `repo sync` is over, run `cd frameworks/support/ui/`
- Here, it is recommended to run custom build of Android Studio provided within this repository. Run `./studiow` to start custom build of Android Studio.
- Let Android Studio sync the project.

### Using skiko lib instead of skija

- Build and publish skiko to local maven using the instructions [here](https://github.com/JetBrains/skiko) 
- Include skiko as dependency in `buiild.gradle(ui-desktop)` as `api("org.jetbrains.skiko:skiko-jvm-runtime-linux:0.1-SNAPSHOT")`
- Modify `androidx/ui/desktop/ComposeInit.kt` from `Library.load("/", "skija")` to `Library.load("/", "skiko")`

### Run

- Run the gradle task `run` in compose-desktop-sample
