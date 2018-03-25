---
title: 'Functional Programming: A Paradox?'
tags:
  - functional programming
  - speculation
id: 494
comment: false
categories:
  - Programming
date: 2011-12-01 09:25:53
---

I like the idea of Functional Programming. It just appeals to my own mathematical bent I think. A few of the central tenets include immutability of data and creating functions that always produce the same results given the same arguments by avoiding side effects. All this to minimize generation of unpredictable results (errors). But wait a minute. There is another field of study with a long and deep history of research, random number generation, that says it is virtually impossible to generate truly random (unpredictable) results on a computer. What gives?

Much research indicates that the generation of truly random (unpredictable) results has to rely on a random physical process outside of the computer, such a radioactive decay or waiting for cosmic rays to randomly flip bits in a computer's memory, or maybe timing the period between malware attacks on you system. (That's an interesting idea I've never seen explored.)

If it's so hard to generate truly random results on the computer, why do FP and other programming methodologies have such strict prescriptions for producing more reliable programs?

Well, in my opinion there are a few differences between generating random numbers and writing error-free programs for more traditional programs, but they all seem to boil down to the amount of complexity and detail we can reliably retain in our feeble little brains. Generally when you want to generate random numbers, you want lots of them in very little time. More traditional programs tend to run for longer periods of time and have to deal with much more data that needs to be retained during the execution of the program.

Avoiding side effects reduces the amount of stuff your unreliable brain has to keep track of. A side effect is just another kind of input that is given to the program. With a given value, the results are entirely predictable. If you can remember them and keep track of all of the possible values of the side effects and assure that you handle them correctly. Without those side effects, you have so much less to keep track of.

Same thing with mutable state. A computation from a given state will always produce the same result. The problem is our inability to reason completely about all of the interactions and effects of mutable state. We've spent the last 50 years or so proving this inability over and over. Immutable state means less stuff to keep track of.

On a fundamental level, there is no theoretical issue with side effects, mutable state or all the other bugaboos of FP and other methodologies. It's simply our inability to deal with the immense amount of variation that causes problems.

Still, it would be interesting to investigate whether the tools used to examine and test the quality of random number generators have any application to testing the quality of other types of programs.