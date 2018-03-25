---
title: A New Version of the Confidence Interval Program
date: 2017-09-13 17:18:01
modified: 2018-03-25T16:13:26.786536-04:00
tags:
  - statistics
  - clojure
  - javafx
categories:
  - programming
---

Recently, I [wrote](http://yo-dave.com/2017/08/31/2017-08-31-an-updated-sign-test-program/) about updating an old program that did the [Sign Test](https://en.wikipedia.org/wiki/Sign_test). Well, I have lots of old programs that could stand a bit of refreshing.

Another of the simple ones calculates the confidence interval around the proportion of successes in a series of [Bernoulli trials](https://en.wikipedia.org/wiki/Bernoulli_trial). I [wrote about it](http://yo-dave.com/2011/06/14/2011-06-14-binomial-confidence-intervals-binomconf/) way back in 2011. The original was written in Java and Swing many years ago. It is still available in a [repository](https://bitbucket.org/David_Clark/binomconf) on Bitbucket. It hasn't been updated in about five years though.

So, I thought it might be fun to make an updated version. This time it's written in Clojure and uses the JavaFX GUI framework. Here's what it looks like now.

[![A New Version of the Binomial Confidence Program](/static/img/2017-09-13-new-confidence-limit-program.PNG "A New Version of the Binomial Confidence Program")<br><small>A New Version of the Binomial Confidence Program</small>](/static/img/2017-09-13-new-confidence-limit-program.PNG)

The interface hasn't changed much, but it does display a progress dialog for the second or so that it might take to calculate the results for some input data. That gave me a chance to play with JavaFX `Task`s and progress dialogs. My usage feels a little clumsy, but it does work.

Also, the statistics calculations have been re-written in Clojure and added to my little library of functions related to the binomial distribution.

Hope you enjoy it.

## Update 3 Nov 2017 ##

Noticed that I forgot to include a link to the repository of the new version of the program. It's [here](https://bitbucket.org/David_Clark/binomial-conf).