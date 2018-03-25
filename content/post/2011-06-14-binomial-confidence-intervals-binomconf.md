---
title: Binomial Confidence Intervals -- BinomConf
tags:
  - java
  - statistics
  - utility
id: 389
comment: false
categories:
  - programming
date: 2011-06-14 16:57:28
---

Way back in my career there was a need to calculate binomial confidence intervals on experiments with very large numbers of trials (thousands to tens of thousands.) The statistics packages of the time couldn't seem to handle such large numbers of trials.

<!--more-->

For example, how often did a particular chemical reaction produce a positive result even when the conditions to produce a positive were not present (a "false positive"). If we ran thousands of these simple reactions, we could get an estimate of how frequently it produced a false positive. But that was just a point estimate. Conversations with managers on the program would go something like:

Mgr: "How confident are you that the false positive rate is 0.27%?"

Me: "Not at all."

Mgr: "What!? What did you just spend all that time and money doing?..."

Me: "All of these measurements are just estimates. What if I told you that I'm 95% sure that the false positive rate is somewhere between 0.25% and 0.29%?"

Mgr: "Oh. Well, that's OK then."

Since the programs available to me at the time could not handle the level of replication we were using, I wrote my own. As I recall, the first version of the program was written in C and ran at the command line. Later versions were written in Delphi and gained a GUI. The current version is in Java. It looks like this.

[![Image of binomial confidence interval calculator](https://github.com/clartaq/yo-dave/raw/master/images/2011-06-14-BinomConf.png "BinomConf")<br><small>Screenshot of the Binomial Confidence Calculator</small>](https://github.com/clartaq/yo-dave/raw/master/images/2011-06-14-BinomConf.png)


Although I still use the program for its original purpose, I also use it as a quick reminder of how to do certain things I do over and over. (I have no memory for small configuration details.) Some of these things include validation of user input, unit testing at the GUI level, saving user preference data, writing help, and so on.

The Java version of the program was written back before IDEs were very good just using a text editor and Ant for the build instructions. It's been updated to a NetBeans project, but the conversion is incomplete. You can build and run the program in NetBeans, but to do unit testing, you have to use the command line and Ant:

`c:\ant test`

Still, it's a handy little utility and I've made it available on BitBucket at

[https://bitbucket.org/David_Clark/binomconf](https://bitbucket.org/David_Clark/binomconf "Link to BinomConf repository on BitBucket.")

Enjoy!