---
author: david
title: clown -- A Clojure/Script Outliner with Notes
date: 2020-01-05T09:39:38.060-05:00
modified: 2020-01-05T16:07:50.992-05:00
tags:
  - Clojure
  - ClojureScript
  - figwheel-main
  - Note-Taking
  - Outliner
---

Questions about note-taking are very popular on [Hacker News](https://news.ycombinator.com). I am always interested in the responses. It seems like developers are always interested in the best tools and methodologies. Me too.

I've been researching and using different approaches for decades. Over all that time, I've written thousands of notes, both short and long. Most notes become uninteresting after awhile and are just erased. But thousands have persisted for many years because I still refer to them.

Outliners are a natural way to organize a body of knowledge. They have been part of my workflow for many years from applying special formatting to Microsoft Word decades ago to OmniOutliner when I started working on a Mac a few years ago.

Despite their strength at thought organization, outliners have one big weakness for me -- visibility. The headlines can get quite long when you need to record lots of information in them. Not everything can be subdivided into subheadings. Sometimes you need to write long-form text. When you do that in an outline, you can lose sight of the overall organization of the document.

So, after that long ramble, what to do? I've started playing with a program I'm calling `clown` (**cl**ojure **o**utliner **w**ith **n**otes). It works like a traditional outliner but with a second pane that can display any notes attached to a particular headline. This lets me retain visibility of the bigger picture, the outliner, while still being able to drill down into very detailed information in the notes.

Here's a snippet of what it looks like.

[![clown](/static/img/2020-01-05-Clown-Screenshot.jpg "A Screen Clip of the clown program")<br><small>clown</small>](/static/img/2020-01-05-Clown-Screenshot.jpg)

You can't see the buttons at the bottom, but they may disappear anyway.

The app supports an outline with any number of headlines and sub headlines nested to any depth. Each headline may have an arbitrary number of notes associated with it. It's very early days, but there is a [depository on GitHub](https://github.com/clartaq/clown) for anyone who wants to take a look at what I'm talking about.