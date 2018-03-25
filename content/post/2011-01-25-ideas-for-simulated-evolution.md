---
title: Ideas for Simulated Evolution
tags:
  - lisp
id: 35
comment: false
categories:
  - programming
date: 2011-01-25 00:30:34
modified: 2018-03-15T10:18:23.580253-04:00
---

**Note**: This is a re-post of an earlier entry recovered from a different blogging system.

After entering and running the `evolution.lisp` example from [_Land of Lisp_](http://landoflisp.com/), I have some ideas for additional traits.

Here are a few.

1.  **Size, Carnivorous Animals**. Add a feature to allow the animals to be of different sizes. Larger animals would expend energy faster. When two animals occupy, the same cell, the larger would try to eat the smaller, taking it's energy. The smaller could try to flee. Chance of successful hunting/fleeing would be based on the size difference. For example, if the larger animal is only slightly larger, the smaller animal would have a pretty good chance of escaping. As the hunter gets larger, the chance of succeeding becomes larger.
2.  **Speed**. With a higher energy expenditure per turn, animals could move more than one cell at a time. Perhaps this would be a modifying factor in fleeing from hunters too. Occaisionally, a miracle happens and the smaller creature eats the larger one. (Ever see a python eat a goat?)
3.  **Sex**. If two animals of the same size occupy the same cell and have enough energy between them, they could reproduce and shuffle their genetic material in the offspring. Number of offspring might be another variation depending on energy.
4.  **Plant Toxicity**. Immature plants would be poisonous. The animals would require some sense(s) to detect the maturity of a plant before eating it. If their senses aren't adequate, they would die, leading to...
5.  **Carcases**. Animals that die from eating poisonous plants would leave their carcases around for some amount of time. Other animals could eat the carcases for additional energy.
6.  **Vision**. One of the senses to detect plant maturity might be vision with variations in acuity at distance (to see plants further away) and color (for detecting plant maturity).

Of course, a nicer user interface would be a good thing too.

