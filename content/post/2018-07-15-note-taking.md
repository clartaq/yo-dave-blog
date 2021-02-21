---
author: david
title: Note-Taking
date: 2018-07-15T16:47:36.659-04:00
modified: 2018-07-15T18:01:42.552-04:00
tags:
  - note-taking
---

Changing jobs in 2005 caused me to switch from taking notes on paper to doing it electronically.

My new employer provided a tablet PC with a stylus running Windows and [OneNote](https://products.office.com/en-US/onenote). Taking notes on that system with excellent handwriting recognition was a revelation. The tablet was pretty clunky by today's standards, but it worked well. It was particularly useful for generating meeting notes in real time and projecting them during meetings.

After moving to another employer, I no longer had access to such a tablet but used [TiddlyWiki](https://tiddlywiki.com) to keep my notes. It was great until the number of notes became large (in the thousands).

After that, I became an early adopter of [Evernote](https://evernote.com). It was an adequate note-taking app, but the killer feature for me was searching for text in images. At that time, we put meeting notes up on a whiteboard. After the meeting, I could take a picture of the whiteboard, insert it in Evernote, and search it any time after that. I maintained a subscription to Evernote until the end of 2016 until they got squirrely about their subscriptions and some privacy issues.

After several years absence, I went back to TiddlyWiki. It was very much improved. But after importing thousands of notes, it became too slow at editing. The search was on for another system.

I looked at several open-source tools under active development:

* [TreeSheets](http://strlen.com/treesheets/) has been around a long time and has some unique features for organizing your information.
* [CherryTree](https://www.giuspen.com/cherrytree/) it has been around for quite some time. Very long feature list. Very long wishlist too. 
* [BoostNote](https://github.com/BoostIO/Boostnote) bills itself as a note-taking app for programmers. It has clients for lots of platforms and can keep them all in sync.
* [iA Writer](https://ia.net/writer) is very focused on writing.

I committed to [Collate](https://collatenotes.com) for awhile, even bought a license, but development seemed to stop shortly after I paid for it. And it keeps asking me to re-enter my license key. So, slowly moving off of it. And no more closed-source apps.

These days, my notes usually start short and then get longer and more detailed over time. Often they include mathematics, so typesetting math is crucial to me. Joplin fills the bill very well. It's almost perfect for me. But linking to other notes is a bit clumsy.

I set up a self-hosted [MediaWiki](https://www.mediawiki.org/wiki/MediaWiki). It did everything I wanted (and way more) but was more cumbersome to administer than I wanted to deal with.

What I finally ended up doing was writing my own wiki. 

* It uses a version of Markdown that can typeset math (_via_ [MathJax](https://www.mathjax.org)) and accepts wikilink syntax as well. 
* Syntax highlighting for program snippets.
* It seems to be cross-platform. (I started development on Windows, now do it on macOS, and can (but shouldn't) self-host it on a Linux server.
* It runs on a desktop. (I can't read long notes with math on a phone and tablets are pushing it a bit with my poor vision.)
* It's written in Clojure and ClojureScript, languages I enjoy working with.
* The editor I wrote has a real-time preview (but the scroll bars don't' sync yet.)
* The editor is compatible with Grammarly, so I get some help with my atrocious spelling and grammar.
* All data is stored locally in a SQL database but can be exported to Markdown files with one keystroke.
* Pages can be exported with YAML so that​ they can be imported directly to my blog.
* and on and on.

Another option that might be useful is a [Jupyter](http://jupyter.org) notebook. It looks interesting, but I don't really have much experience with it. There's even a [kernel for Clojure](https://github.com/clojupyter/clojupyter) that I want to check out.

If you have the interest and the desire, I encourage you to look into writing your own tool. It isn't as hard as you might imagine just to get something working. It is incredibly hard to polish things to just the shininess you might want, but that's part of the fun.