---
layout: post-no-feature
title: "Clarity Keyboard - Built with Clojure"
description: ""
category: articles
comments: true
tags: [clojure, webview, clarity, programming]
---

This week, here at [SwiftKey](http://swiftkey.com), we released [Clarity Keyboard Beta](https://play.google.com/store/apps/details?id=com.swiftkey.clarity.keyboard&referrer=utm_source%3Dadamblog%26utm_medium%3Dblog%26utm_content%3Dprogrammingpost) - an experimental keyboard that we'll be using to test out some new keyboard concepts over the coming few weeks and months. It's been built from the ground up and uses a host of bleeding-edge technologies as well as an interesting and unusual choice of programming language: Clojure.

## What is Clojure?
[Clojure](http://clojure.org/) is a functional programming language that, like Java, targets the JVM. 

For the uninitiated a language is 'functional' because it encourages the use of 'pure' functions (no side-effects - i.e. no state) and immutable data. Contrast this with an imperative language like Java in which the use of member variables (state) that can be updated many times during a program's execution are the norm. One of the biggest benefits of this approach is that multi-threaded programming is much easier and safer if you don't have to worry about mutable state (ie variables being updated by multiple threads, potentially simultaneously and/or unpredictably). It also makes chunks of code easier to reason about without having to understand all of the context in which it is used. 

Another great thing about Clojure is that because it's a dynamic language functions can be hot-swapped out for others while the application is still running, enabling the software engineer to try out newly written code almost instantaneously without waiting for the application to be recompiled. This increases productivity and speeds up development time.

I won't list the many other benefits of using Clojure here - you can read a more extensive rationale on the [Clojure website](clojure.org/rationale). Not everything is rosy though, there are some big drawbacks to using Clojure too. The main one being that it has historically had a very long initial load time. The time to load a Clojure application is often counted in seconds!

As a result of all of this Clojure has tended to be found being used to build server infrastructure where a long initial load time is a reasonable tradeoff for its other benefits. It has rarely, if ever, been used to build consumer mobile applications.

## Clojure on Android! (not all roses)

### Build tools

Unfortunately, Android support isn't quite that simple. All of the Android build tools assume that you are using standard Java, in a standard IDE, and that you won't wander too far from the beaten path. 

Luckily for us, Daniel Solano Gomez and Alexander Yakushev among others have done most of the hard work for me already as part of the [clojure android](clojure-android.info) group on GitHub. This includes lein-droid, a plug-in for the leiningen build system which deals with turning clojure code into an apk, packaging resources etc. and a special build of clojure which can actually generate android dalvik byte code at runtime, allowing for the dynamic behaviour mentioned above.

Having said this, when looking to make a production-quality app, there were some aspects which simply hadn't been covered before and so some tool support was missing for simple things like version code generation, bundling of native libraries, Proguard optimisation and Multi-dex (which allows you to have more than 65000 method references). lein-droid is open source and clojure is easy to read though, so it was perfectly possible to add these features and push them upstream so that they are now part of the official version.

### The unbeaten path

Clojure ART issues, synchronisation bug, load times? skummet?.


As you might expect, the issue of the long load time was the biggest barrier to using Clojure to build a mobile application. How long would you wait for an application to load on your mobile phone before thinking it's crashed? However with the help of the key contributors to some of the open-source libraries we're using we've reached a point where using Clojure to build a mobile application in production is absolutely feasible. Clarity Keyboard's initial loading time is around 1.5-2 seconds and there are still a number of improvements we plan to make to get this down well below a second.

### REPL!

Read, Evaluate, Print, Loop - LISP talk for being able to tweak your program on the fly. This really helps with the development - especially when making subtle changes to the user experience. Someone can be using the keyboard, spot something wrong and change it to try the new value without even hiding the keyboard from the screen!

## Design decisions made/influenced by Clojure

### core.async

Based on CSP - this means that everything is asynchronous from the ground up but also responsive

### Event Driven

### Modular


*We'll be publishing more detailed posts about our experience building mobile applications in Clojure on Android; technologies we've used such as [test.check](https://github.com/clojure/test.check), [Clojure on Android](http://clojure-android.info/) and [core.async](https://github.com/clojure/core.async); REPL-driven development; Clojure itself and the Clarity project over the next few weeks and months. If this interests you let us know and we'll write more!*

Clarity Keyboard Beta is available on the Google Play store now - try it out and let us know what you think.

<a href="https://play.google.com/store/apps/details?id=com.swiftkey.clarity.keyboard&referrer=utm_source%3Dadamblog%26utm_medium%3Dblog%26utm_content%3Dprogrammingpost">
<img alt="Get it on Google Play" style="width:150px" src="https://developer.android.com/images/brand/en_generic_rgb_wo_45.png" />
</a>


