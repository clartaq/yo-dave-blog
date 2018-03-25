---
title: Wilcoxon Matched Pairs
tags:
  - Java
  - Statistics
id: 484
comment: false
categories:
  - Programming
date: 2011-11-14 18:32:38
modified: 2018-03-16T10:40:08.928905-04:00
---

At my previous employer, our goal was to stain tissue samples such that a pathologist could examine them microscopically and easily make unambiguous diagnoses of disease state. Experiments typically involved getting subjective judgments from pathologists about which samples were "better" in some way. How do you do statistics on those type of results?

<!--more-->
When comparing the results from a control condition with the results from a single experimental condition, the results would often come back as "good" _vs_. "bad", "better" _vs_. "worse", "unacceptable" _vs_. "acceptable" _vs_. "exceptional" or a similar ranking. In some parts of the business, it was customary to provide the rankings in a pseudo-numeric form -- this stain has an intensity of 3.5 while this other stain is a 4.0\. But even though the scores _look_ like they are on an [interval scale](http://en.wikipedia.org/wiki/Level_of_measurement "Levels of measurement"), they are actually ordinal. For the same scorer, the difference between a 3.5 and 4.0 may not be the same as the difference between a 2.5 and a 3\. And different scorers can give different results.

Before coming to work for this employer, I had always done experimentation where the results were on an interval scale. I was initially at a loss as to how to deal with the ordinal results I had to work with. After exploring my options, one analysis method I have come to enjoy is the Wilcoxon Matched Pairs analysis (also known as the [Wilcoxon Signed-Rank Test](http://faculty.vassar.edu/lowry/ch12a.html "Wilcoxon Signed-Rank Test"), not to be confused with the [Wilcoxon Rank-Sum Test](http://en.wikipedia.org/wiki/Mann-Whitney-Wilcoxon_test "Wilcoxon Rank-Sum Test") or Mann-Whitney U Test. I tend to say "matched pairs" to keep myself and others from getting confused.) The test estimates how likely it is that two sets of correlated samples have the same median.

The experimental set up is easy, matched pairs of control and experimental samples. It's a non-parametric test that makes few assumptions about the underlying distribution from which the data is drawn. It's easy to explain to scientists without much statistical background.

So, I created a tool to help myself and my colleagues analyze such data. It's written in Java and available on a [public repository on bitbucket](https://bitbucket.org/David_Clark/wilcoxon-matched-pairs "Wilcoxon Matched Pairs Repository").

Here's a screen shot of the application after it starts up:

[![Image of the Wilcoxon Matched Pairs program startup screen](https://github.com/clartaq/yo-dave/raw/master/images/2011-11-14-Wilkoxon-Matched-Pairs-Start.jpg "Image of the Wilcoxon Matched Pairs program startup screen")<br><small>Wilcoxon Matched Pairs program startup</small>](https://github.com/clartaq/yo-dave/raw/master/images/2011-11-14-Wilkoxon-Matched-Pairs-Start.jpg)


The interface displays an area for data input. Although data can be entered here, it is really intended that data be loaded from a specially formatted data file, as explained in the "readme.md" file in the repository. (This is an example of my dissatisfaction with data entry components available for Java as elaborated [here](http://yo-dave.com/2011/11/14/2011-11-14-wilcoxon-matched-pairs/ "Dissatisfaction with Java data grid components").)

Once a suitable data file is loaded, the data and some metadata are available for inspection.

[![Image of the Wilcoxon Matched Pairs program after loading some data](https://github.com/clartaq/yo-dave/raw/master/images/2011-11-14-Wilkoxon-Matched-Pairs-VassarDataLoaded.jpg "Image of the Wilcoxon Matched Pairs program after loading some data")<br><small>The Wilcoxon Matched Pairs program after loading some data</small>](https://github.com/clartaq/yo-dave/raw/master/images/2011-11-14-Wilkoxon-Matched-Pairs-VassarDataLoaded.jpg)

In this example the data appears to be on an interval scale, but in fact, it is ordinal. The program also shows the number of data pairs (useful for larger datasets) and there is a drop down that shows all of the valid scores in order.

A click on the "Analyze" button does the work.

[![Image of the Wilcoxon Matched Pairs program after analyzing some data](https://github.com/clartaq/yo-dave/raw/master/images/2011-11-14-Wilkoxon-Matched-Pairs-VassarDataAnalyzed.jpg "Image of the Wilcoxon Matched Pairs program after analyzing some data")<br><small>The Wilcoxon Matched Pairs program after analyzing some data</small>](https://github.com/clartaq/yo-dave/raw/master/images/2011-11-14-Wilkoxon-Matched-Pairs-VassarDataAnalyzed.jpg)

The analysis shows the number of usable (non-tie) data pairs as well as some intermediate values in the calculations. The value of p is used to judge the significance of the difference in medians. In this case, the value of 0.035 is usually considered to be significant.

This program operates a little differently than similar ones in that most programs calculate the exact p value for small numbers of observations, but use a normal approximation for larger numbers of observations. This program uses the exact calculation for all numbers of observations. In my own work, even when there are thousands of pairs of data, the exact calculation occurs almost instantly, so why add the additional complication.

The code in the repository is a [NetBeans](http://netbeans.org "Link to the NetBeans IDE web page") project. This project was created with an old version of NetBeans, but has been run and tested with NetBeans 7.0.1\. However, the program was created with the [Swing Application Framework](http://en.wikipedia.org/wiki/Swing_Application_Framework "Wikipedia article on the Swing Application Framework") and NetBeans 7.0.1 is the last version to support the abandoned framework. The project won't compile with NetBeans 7.1 or later. What a pain. Perhaps future work will continue by adapting the project to the [Better Swing Application Framework](http://kenai.com/projects/bsaf/pages/Home "Link to the Better Swing Application Framework")(Broken Link). Or maybe it's finally time to start learning the [NetBeans Platform](http://netbeans.org/features/platform/ "Link to the NetBeans Platform project") for ongoing desktop development work.
