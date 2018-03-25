---
title: An Embarrassment of Riches for Note-Taking
date: 2017-04-03
tags:
  - note-taking
  - writing
---

Ever since [dumping Evernote](https://clartaq.github.io/yo-dave/2016/12/15/2016-12-15-de-activating-my-evernote-account/), I've been looking at alternative note-taking apps. I've been using[ tiddlywiki](http://tiddlywiki.com/), but, surprisingly, it has grown a bit slow. Probably from the sheer volume of notes that I've imported from Evernote. Maybe time to look some more?

I've also been writing a lot of program documentation in Markdown. I like the wiki format just fine, but it seems like [Markdown](https://en.wikipedia.org/wiki/Markdown) can do more. Both markup languages have a plethora of dialects, usually incompatible. I have been a paid customer of [MarkdownPad2](http://markdownpad.com/) on Windows for awhile now. It is perfectly capable, but has not been updated in a few years (although the website keeps getting updated) and the preview pane can respond slowly after a syntax highlighted code block has been added. So, I've been doing more and more work using an editor I've written for my own use.

Since I've been doing so much work in Markdown, I thought maybe it's time to look at a note-taking app that uses Markdown as it's native editing markup. Just as there are zillions of Markdown editors, there are zillions of note-taking apps that use Markdown to edit their notes.

Here a three you may not have heard about.

## Boostnote ##

[Boostnote](https://boostnote.io/) is an open-source note-taking app that bills itself as a "Note-taking app for programmers" It is also probably the least polished of the three. That isn't a slam against it, just an observation. It has a "traditional" note-taking layout and a pleasant interface. It lets you categorize your notes using tags. You can also specify where you want your notes stored. For example, I have one group of notes that I keep synced with a NextCloud remote server. I keep another group of notes on my local hard drive.

Editing starts by clicking the note you want to start working on. The editor switches to a highlighted plain text view where you can just start typing away. You can add formatting by typing in the usual Markdown markup characters or insert them _via_ menu (which just adds the characters in the editor.) When you click outside of the editor, the view switches to a formatted version. Switching back and forth between the two views can be a bit unnerving, but works well and quickly. The app apparently does not do spell-checking of any sort.

Boostnote also supports adding LaTeX into your notes. Something that used to be pretty rare. But two of the apps I'm talking about here today have that feature. 

Boostnote has an interesting, programmer-directed feature in that it can act as a "snippet" organizer. That is, it can create notes that are much like Github [Gists](https://gist.github.com/).

Operation is a little quirky, but this is a project to keep an eye on. It is under active development and updates regularly.

## Typora ##

[Typora](https://www.typora.io/) takes a very minimalist approach to note-taking. It is really more of an editor for writing with no organizational tools. You just read and write files from/to disk. In this case, the editor is more WYSIWYG -- very few distractions -- no preview pane or switching back and forth between editing and preview modes.

It handles things that are important to programmers with aplomb. It handles syntax highlighting well and supports a large number of programming languages. One advantage it has over MarkdownPad2, for me anyway, is that it continues to be responsive to editing even after including a large, syntax-highlighted piece of code. And it does spell-checking.

Typora does have the option of displaying an outline of a loaded document based on heading levels in the document.

One somewhat unique feature supported by Typora is entry of mathematical expressions _via_ [MathJax](https://www.mathjax.org/). It's just so convenient when you need to enter an equation or two but don't want to fire up a full-fledged LaTeX editor. There is even a nice preview pane as you type in your math.

Typora is not open source and, in a few places, says it is in beta. Don't know if it will always be free.

## Milanote ##

Milanote is a different sort of note-taking app. It let's you compose "Boards" out of notes and images and so on, collectively called "Cards", arranging them in various ways. You can aggregate information from various sources on a board of apparently infinite size. You can add arrows to explicitly show relationships. It feels very natural to organize boards by project this way.

You can also organize material in "Columns" that support adding any number of Cards. Cards may be text notes or images or anything else you would put on a board.

All of these elements can be moved around at will as you get a better idea of how to organize the little bits of information. If you have arrows linking columns or notes, etc, the arrows move, extend, and contract to maintain the relationship shown.

One weird thing I noticed was that I was not able to change the width of text notes. They expanded automatically in the vertical direction, but were all a fixed, identical width that I could not figure out how to change. Images were easily re-sizable.

The program doesn't seem to support code blocks in the view of a board, but you can export a board to Markdown, where it will be syntax highlighted correctly if you use Github style fenced code blocks.

Milanote is not open source and is a web app, keeping your data on their servers somewhere. If you are comfortable with that, great.

The beta is free, but has a limit of 100 cards. There are "team" features for collaboration, but I did not explore those at all.

## Conclusions ##

There is no shortage of note-taking apps with more appearing all the time. Boostnote and Typora are desktop apps written using the [Electron](https://electron.atom.io/) framework (with JavaScript and Node, etc. underlying it all). As such, they are cross platform. Milanote, as a web app, is inherently cross platform too.

All are relatively young and under active development. The futures of the two "beta" programs, Typora and Milanote, are unknown. Costs of future versions are not clear. Boostnote apparently has commercial aspirations as well, with a Team version "Coming soon". However, since it is open source, you don't really have to worry about the company going defunct and loosing your data.

Typora is by far the best Markdown editor I have ever used. I might actually pay money for it someday, provided it is not one of those yearly licensing arrangements.

But for now, despite it's lack of polish and few weirdness's, I think I be using Boostnote for awhile.

