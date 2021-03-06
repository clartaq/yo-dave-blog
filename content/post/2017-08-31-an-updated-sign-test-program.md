---
title: An Updated Sign Test Program
date: 2017-08-31 13:05:51
modified: 2018-03-27T11:34:55.076093-04:00
tags:
  - statistics
  - clojure
  - javafx
categories:
  - programming
---

Long ago, I wrote a [post](https://yo-dave.com/2011/07/09/the-sign-test/) about a small program to calculate the probabilities of a sign test.

A lot has happened since then.

The sign test is still useful to me on occasion, but the application framework used to write the original program is now unsupported. Too, the original program used Java's Swing framework for the GUI. The new official GUI framework for Java is JavaFX.

So I've updated the program a bit. Here's what it looks like now.

[![A New Version of the Sign Test Program](/static/img/2017-08-32-new-sign-test-program.PNG "A New Version of the Sign Test Program")<br><small>A New Version of the Sign Test Program</small>](/static/img/2017-08-32-new-sign-test-program.PNG)

 It looks similar and produces exactly the same results, but

- It is now completely written in Clojure. The original was an experiment in Java/Clojure interop.
- It uses JavaFX for the GUI.
- It uses a different build tool, Leiningen.
- It has different dependencies, a couple of other libraries I have written.

The old repository has been completely removed, but the [new one](https://helixteamhub.cloud/Regolith/projects/binom-stats/repositories/signtest/tree/default) has the same name, so all the old links should now point to the new library.
