---
author: david
title: My Note-Taking Journey
date: 2019-01-06T14:55:42.225-05:00
modified: 2019-01-07T12:52:58.342-05:00
tags:
  - note-taking
---

I'm surprised by how often questions like this come up on HN, but I always find the responses interesting and educational.

## What I Want ##

Non-negotiable items include:

- Low Friction: A little initial setup is ok, but just taking a note should require no more than a click or two (whether clicking a mouse or a pen).
- Open source.
- Aesthetically Pleasing: I'm tired of looking at ugly stuff.
- Has to handle LaTex.
- Has to handle syntax highlighted code listings.
- Notes are in an easily portable format.
- Organization Tools: Some sort of organizational ability. I have thousands of notes that I use and need to refer back to all the time.
- Privacy: No sending my personal information to someone else's server.

Negotiable:

- Mobile Friendly: My eyesight isn't good enough to use a phone for note-taking or reading. Tablets are Ok. Desktop is best for me.
- Implementation Language: Since I'm looking for an open source solution, I'd like it to be in a language that I like. In my case, Clojure/ClojureScript, Lisp, Scheme (Racket) would be optimal. JavaScript is least desired.
- Minimal dependencies: I'd prefer that it not be based on Node, NPM, Electron, Atom, or any other huge ecosystem that would have to be installed on my system. (Java is Ok with me, obviously.)
- Scriptability: It would be nice if it could help automate my workflow.

## What I Have Tried ##

- Long ago (2005) I started a job at which the company provided me with a stylus-based, Windows tablet PC. Included was a copy of [OneNote](https://products.office.com/en-us/onenote/digital-note-taking-app) that could do very good, real-time handwriting recognition.

   During work meetings, I typically created the notes as the meeting progressed and projected them on a screen in the meeting room, letting everyone make sure that what they said was understood and that they could see what they had agreed to (tasks, dates, etc.)

   When I left that job, I would have bought one of those tablets if I could have afforded it. Since then, OneNote has morphed into something unusable for me and I haven't gone back.

* I was a very early adopter of [Evernote](https://evernote.com/) and had a premium membership for a long time. I really missed the ability to do handwriting recognition, but being able to search within images was almost as useful. We would put meeting minutes up on the whiteboard, photograph them at the end and stuff them into Evernote so they could be searched.

   I sadly stopped using it after Evernote also morphed into some unusable mess (Pay more! Get less!) that I couldn't remember how to use it on different platforms.

* I used [TiddlyWiki](https://tiddlywiki.com/) for a while. It was quite powerful and easy to use, but it just bogged down unacceptably with a lot of notes.

* [Zim](http://zim-wiki.org/) wiki works well, but it doesn't have all of the features I want these days. And it is so ugly that I just can't bear to use it.

* [WikidPad](http://wikidpad.sourceforge.net/) is nice but also ugly and missing features that I need these days. And I don't use Windows anymore.

## What I Do Now ##

I have tried to consciously move away from Google and Microsoft tools and closed-source programs (not always successfully.)

* I finally just wrote a personal wiki of my own. I started it in [Racket](https://racket-lang.org/), but moved on the [Clojure](https://clojure.org/) for server part and [ClojureScript](https://clojurescript.org/) for the client part, mostly just the Markdown editor. It creates documents that are based on [Markdown](https://en.wikipedia.org/wiki/Markdown), but include extensions for [LaTeX](https://www.latex-project.org/), syntax highlighting, [YAML](https://yaml.org/) front-matter, and, of course [Wikilinks](https://en.wikipedia.org/wiki/Help:Link).

   It exports to Markdown files that can be included directly in my blog.

   It has almost all of the features I want and it is relatively easy for me to add new things as needed.

* I always keep an inexpensive [sketchbook](https://www.strathmoreartist.com/visual-journals/visual-journal-watercolor-140-lb.html) with me for drawing, painting (watercolor, tempera, and acrylics only) as well as note-taking.

   Scanning or transcribing notes into my wiki was a PITA, but...

* I have recently started using a [Rocketbook](https://getrocketbook.com/). I use it for detailed drawings at my desk. But for things that are in my sketchbook, I can place the smaller pages from the sketchbook onto a page in the Rocketbook and scan them. They are then sent off to my email account. There are settings to cause the handwritten notes to be transcribed and placed in the email, allowing easy transfer into the wiki. (You don't even need to buy a Rockebook to do this. They offer free, printable pages on their [web site](https://getrocketbook.com/blogs/news/5-sane-rational-and-practical-reasons-why-we-offer-free-rocketbook-pages).)

   That's usually all I need.

* For web clipping, I use the browser extensions from [Joplin](https://joplin.cozic.net/) or [DEVONthink](https://www.devontechnologies.com/products/devonthink/overview.html).

* For extremely long-form writing (beyond a blog post or note), I usually start with my wiki, splitting the major parts into different documents. While collecting research, I use DEVONthink and [Scrivener](https://www.literatureandlatte.com/scrivener/overview). Finally, I put it all together in Scrivener.

## The Future ##

* I enjoy expanding my wiki, both in content and in function. I want to add sophisticated tagging, something like in [Trilium](https://github.com/zadam/trilium).

* I think [OPML](https://en.wikipedia.org/wiki/OPML) is the perfect format for writing a daily journal among other things.

* Of course, there is a never-ending list of smaller things to add.