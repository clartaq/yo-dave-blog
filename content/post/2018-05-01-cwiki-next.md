---
author: david
title: CWiki-Next
date: 2018-05-01T09:38:50.298332-04:00
modified: 2018-06-09T11:11:20.993936-04:00
tags:
  - Clojure
  - ClojureScript
  - MathJax
  - wiki
---
For the past few months, I've been beating my head against a brick wall. The problem was that I was trying to get an all-server-side wiki built using [Clojure](https://www.clojure.org).

It actually works pretty well. I've been using it for personal information for months now. It works well except for one aspect, arguably the _most_ important -- editing new or existing content is not that pleasant.

It's all a variation of [Markdown](https://daringfireball.net/projects/markdown/), which is nice. It's slightly modified to handle mathematics _via_ [MathJax](https://www.mathjax.org), and since it's a wiki, it needs to handle wiki links. I use the MediaWiki style. It all works. Except there is no live preview as you type. Right now, you have to save the file and load it into the wiki to see what it will look like. It's a matter of seconds, but still a break in the flow.

So, I've decide to just bite the bullet and split the application into a client-server configuration. The server will remain in Clojure, handling all the database stuff. The user interface will render on the client side using [ClojureScript](https://clojurescript.org).

I got inspiration from Carmen La's Live Markdown Editor. That's pretty much what I want with a toolbar added. I also saw a couple of articles on how to use AJAX (web newbie here) and something finally clicked.

Luckily, I will be able to use a lot of what I already have. The database stuff will remain largely unchanged. The page layout stuff is almost all done with Hiccup and should transfer to Hiccups easily. We'll see.

Progress reports to follow.

