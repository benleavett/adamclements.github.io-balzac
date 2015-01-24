---
layout: post-no-feature
title: "Quicker core.async bootstrapping"
description: "Finding out why core.async adds 10s to my application's bootstrap time and how to remedy it"
category: articles
tags: [clojure, coreasync, programming]
---

# Problem
I have been working on an android application which uses a core.async based event bus at its core (a subject for another post). This works fantastically, but takes an absolute age to load on first run - in the order of 20 seconds. This week I've spent some time trying to work out why that is and do something about it.

One of the main issues I came across was that when `require`ing `core.async` pulls in the massive clojure.tools.analyzer, even in an AOT (Ahead Of Time) compilation. This shouldn't be necessary, because the analyzer is only used in macros and none of that code should be necessary at runtime. The reason it loads is that Clojure doesn't have any separation between runtime code and macros when it comes to namespacing, so when `clojure.core.async` is `require`d, which contains the go macro and friends, `clojure.core.async.impl.ioc-macros` needs to be pulled in so that it can work at compile time.

# Solution
My initial solution was to set `ioc-macros` to `^{:skip-aot true}` in the namespace form, but its dependencies on tools.analyzer were still loading. Then I tried moving the require to compile time so that it wouldn't resolve at run time at all:

```clojure
(defmacro ignore-me-if-aotd [] 
  (require '[clojure.tools.analyzer :as an]
           '[clojure.tools.analyzer.ast :as ast]
           '[clojure.tools.analyzer.jvm :as an-jvm])
  nil)

(ignore-me-if-aotd)
```

And this did the trick. The code compiles no problem and doesn't even generate class files for tools.analyzer and its transitive dependencies.

# Issues

There is one remaining problem with this, and that's REPL support. If you now attach a repl and try to evaluate a go block or any of the macros depending on this, it will fail. For the moment I have simply made two builds - one core.async.runtime and one core.async which I use for development and bear the startup time. Longer term however, I could potentially see a solution where the require form is moved inside the `(defmacro go ...)` itself, allowing the dependencies to be packaged but not loaded unless needed. This would incur a little extra cost at compilation time and incur some complexity overhead as there might be more than one entry point to this code and backtracking what dependencies would be required isn't totally trivial.

# Future enhancements

Clojurescript doesn't have this problem because its macros are separated by necessity, not having runtime code evaluation at all.

Ideally I would like to see something like Clojurescript's `(:require-macros ...)` form in clojure, whose implementation simply added to a list of namespaces to require if anything tries to evaluate a macro and otherwise does nothing. There's no reason I can think of that this couldn't be totally compatible with Clojurescript's require-macros form so that the same code could work in both situations without need for feature expressions.
