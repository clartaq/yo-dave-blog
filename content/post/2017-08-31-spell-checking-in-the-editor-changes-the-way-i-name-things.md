---
title: Spell Checking in the Editor Changes the Way I Name Things
date: 2017-08-31 14:16:24
modified: 2018-03-17T13:14:27.787133-04:00
tags:
  - intellij idea
  - cursive
  - editors
categories:
  - programming
---

While editing some program text recently, I noticed myself doing something weird. I do most of my programming in Clojure these days. I mostly use [IntelliJ IDEA](https://www.jetbrains.com/idea/) with the [Cursive](https://cursive-ide.com/) plugin for Clojure. This turns out to be a great way to develop.

One of the (many) things I love about IntelliJ IDEA is that it does spell checking in my code. Stops me looking (so much) like an idiot when I misspell stuff in comments. As you would expect, it can produce lots of false positives with the strange names programmers use for variables and such. When you misspell a word, it uses a subtle squiggly green underline to mark the offender. It doesn't count misspellings as errors or warnings. And it doesn't put markers for lines with misspelled words in the margins.

Nevertheless... little squiggly green lines. Let's call them LSGLs from now on.

I've found myself changing the way I name things in order to avoid those LSGLs. For example, I might want to `require` a preference persistence library and alias it as `prfs` like this:

```clojure
(ns ...
  (:require
            ...
            [percettings.core :as prfs]
            ...
```
but that produces an LSGL. Changing `prfs` to `prefs` is Ok, but a little long. Changing it to `prf` works since the spell checker apparently doesn't look at words consisting of three or fewer characters. So, that's what I do.

```clojure
(ns ...
  (:require
            ...
            [percettings.core :as prf]
            ...
```
And I have found myself doing this over and over in my code now.

I suspect that says more about me than the utility or operation of the spell checker.


