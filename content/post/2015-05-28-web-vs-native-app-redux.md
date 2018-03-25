---
title: Web vs. Native App Redux
date: 2015-05-28
tags:
  - clojure
  - java
  - javascript
  - meteor
  - web development
categories:
  - programming
---

If you have been following along for awhile, you may have noted how conflicted I am about writing web apps _vs_. native apps. I've been looking into [Meteor](https://www.meteor.com/), which makes the decision even thornier for me.

<!--more-->

There are pros and cons for each approach. I tend to write applications in [Java](https://www.java.com/en/) (or [Clojure](http://clojure.org/)) so portability is not a concern for me, at least for the type of programs I write. Except for phones and tablets. Personally, I don't use a smart phone anymore. A plain ol' flip phone works best for me. But I do use a tablet, an [iPad](https://clartaq.github.io/yo-dave/2011/04/09/2011-04-09-me2-ipad2/). Java doesn't run there easily. And I don't want to have to learn a whole new programming ecosystem just to support that device. So, maybe a web app would be nice.

Also, with [JavaFX](http://www.oracle.com/technetwork/java/javase/overview/javafx-overview-2158620.html) anointed as the next GUI framework for Java apps, things get a little more confusing. JavaFX stuff can look and work brilliant. But it is still buggy. I'm constantly having to introduce some kludge into my code to deal with inadequacies or outright bugs.

But web apps typically require [JavaScript](https://en.wikipedia.org/wiki/JavaScript), which is ugly, _ad hoc_, and baroque in my opinion. [ClojureScript](https://github.com/clojure/clojurescript) might be an option, but it still seems to be a moving target. Maybe [CoffeeScript](http://coffeescript.org/) is an alternative. Maybe [Go](https://en.wikipedia.org/wiki/Go_%28programming_language%29)? And [Rust](http://www.rust-lang.org/) is looking very interesting.

What has confused me more recently is the Meteor framework. It is really quite lovely. At least when working through some of the posted tutorials. It can be made to look good and provides responsiveness almost invisibly. Deployment seems quite simple, although I haven't tried putting an application on any of my own servers.

The thing that holds me back, besides the JavaScript part, is the size of the package I would deliver to clients. Meteor works so well because it integrates a number of different frameworks and tools. It's just a huge amount of stuff that you now don't have to worry about so much during development. But your users also have to have that big pile of stuff delivered to their system when they use your app. And that can make things look slow. And with this [framework madness](http://www.allenpike.com/2015/javascript-framework-fatigue/), I'm not sure how stable all of this really is.

So,... still conflicted. Suffering from an abundance of choices. None of them are perfect. But considering what my users do, I think I'll stick with native applications rather than pursuing continually changing web technology. It feels as though all that stuff exists because it's trying to bend the browser to do something it wasn't intended to do.