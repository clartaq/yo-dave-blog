---
title: Write the Manual First
tags:
  - design
id: 436
comment: false
categories:
  - programming
date: 2011-08-22 16:16:52
---

Back when I was involved in designing medical devices, I had a saying to help guide the design: "Write the manual first." This seemed to be an easier way for the teams to clearly describe what the device should do. Once everyone had a good feeling for how the system should operate, it was easier to move on to the more formal requirements management. Surprisingly, not everyone on the team was as fascinated with that as the Systems Integration team ;-)

The same thing applies to software. There have been [posts](http://jameso.be/2011/08/19/coding-backwards.html "Coding Backwards") about working with the API of a program before the API is actually written. Makes sense to me. It's just another approach to [top-down design](http://en.wikipedia.org/wiki/Top-down_and_bottom-up_design "Top-Down Design WikiPedia page").

The approach has a couple of advantages. First, in terms of requirements elucidation, you get involvement from team members who have no training or interest in the formal process of requirements management. They don't have to figure out what the final design will do based on a series of isolated requirements statements. They get a more holistic view of the product.

Second, it seems to lead to better architectural designs. If you start coding up the low-level classes based on your intuition, that often leads to attempts to fit the final design to a bunch of low-level architectural decisions that weren't quite right to begin with.

This approach was not always taken on teams I've worked on, but on the occasions when it was, it has lead to simpler, more reliable designs in less time. It might work well for you too.