---
title: Getting Started with Lisp/Scheme/Clojure
tags:
  - clojure
  - emacs
  - enclojure
  - lisp
id: 193
comment: false
categories:
  - programming
date: 2011-02-25 16:10:29
modified: 2018-03-15T17:41:06.719515-04:00
---

Ya know, this point just keeps slapping me in the face. It seems that people don't stop trying to use Lisp because they don't like the language. A lot of people stop because they don't like the programming environment. Looking around the Q&amp;A sites there seem to be many more questions about setting up a programming environment for the Lisp family of languages than there are for the more mainstream languages like Java and C++. Well, here are my suggestions.

For Scheme, get [Racket](http://racket-lang.org/ "Link to the Racket language web sit") and use [DrRacket](http://docs.racket-lang.org/drracket/ "Link to DrRacket programming environment documentation") for as long as it's doing what you need. Once (if) you reach the limits of DrRacket, switch to emacs.

If you're coming to [Clojure](http://clojure.org/ "Link to Clojure language web site") from [Java](http://www.oracle.com/technetwork/java/index.html "Link to Java developer site at Oracle"), which seems to be a common progression, you can start with an IDE and a plugin: [NetBeans](http://netbeans.org/ "Link to NetBeans web site") and [enclojure](http://www.enclojure.org/ "Link to enclojure web site")(Broken Link), [IDEA](http://www.jetbrains.com/idea/ "Link to IntelliJ IDEA web site") and ~~[La Clojure](http://plugins.intellij.net/plugin/?id=4050 "Link to La Clojure plugin site")~~ [Cursive](https://cursive-ide.com), [Eclipse](http://www.eclipse.org/ "Link to Eclipse IDE web site") and [Counterclockwise](https://github.com/ccw-ide/ccw/wiki/GoogleCodeHome "Link to Counterclockwise IDE plugin site"). I've tried them all and while it's easy to get started, they tend to be unstable in my experience. And you quickly run into their limitations.

So, unless you're using Racket, I think the path of least total pain is to use emacs. Just do it. You're going to end up there anyway. Take your medicine now. Swallow that bitter pill.

It used to be that setting up a useful work environment in emacs was itself a major roadblock, or at least a very high speed bump. But that has gotten much easier. Use [Lisp Cabinet](http://lispcabinet.sourceforge.net/ "Link to Lisp Cabinet web site"). It sets up a nice environment for several Lisps, Clojure, and Racket. (I haven't used the Racket environment much though.) All the goofy, arcane configuration stuff is taken care of, at least enough to let you get started programming rather than configuring.

Using emacs isn't as bad as you've heard. I've pondered about what finally got me over the hump and I think it was learning a useful, small, subset of the emacs keyboard shortcuts. Right here, in front of your eyes, I will commit heresy and recommend that you **don't go through the emacs tutorial**. Don't waste memory space learning new keyboard shortcuts to move up and down lines, back and forward characters, up and down paragraphs and pages. Any recent emacs has key mappings that let you use the cursor keys on any modern keyboard. And turn on the settings to let you use the CUA keys for copying, cutting, and pasting that you already know. The cursor, end, home, page up, and page down keys may be a little less efficient than their traditional emacs counterparts since you may need to move your hands away from the home position, but you aren't trying to learn emacs (yet). You're trying to become proficient in a new programming language.

Once you get some experience with the Lisp of your choice, then it will become _fun_ to start fiddling with the emacs configuration, since it's another Lisp to work with! Once your brain has cooled off a bit and you are so proficient in your new chosen language that the keyboard is a bottleneck in the process of putting your thoughts down, _then_ learn the more efficient emacs keystrokes that never require you to move your hands away from the home position. Or maybe by that time speech recognition will be so good you won't be using a keyboard at all.

So what is the magic subset of keyboard shortcuts that will get you working in your new language? Your set may differ, but the ones that worked for me are:

*   Open file: `Ctrl-X Ctrl-F`
*   Save file: `Ctrl-X Ctrl-S`
*   Compiling (actually [SLIME](http://common-lisp.net/project/slime/ "Link to SLIME home page") commands)

    *   Compile function: `Ctrl-C Ctrl-C`
    *   Compile file: `Ctrl-C Meta-K`

*   Some of the buffer switching/searching/manipulating commands

    *   Switch to REPL buffer: `Ctrl-C Ctrl-Z`
    *   Previous buffer: `Ctrl-X Ctrl-Lft`
    *   Next buffer: `Ctrl-X Ctrl-Rht`

*   Quit: `Ctrl-X Ctrl-C`

That's it.

Then watch some videos of folks doing something interesting. You'll see them do something really fast that you've been wasting time on. You'll be annoyed that it takes you several seconds to do something that they can do instantly. You'll find that command, start using it, memorize it down into your muscle memory and then ... forget all about it. It'll be embedded in your bones.

So, summing up:

*   Get emacs (or DrRacket). You're going to end up there anyway. **Use LispCabinet**.
*   Ignore the emacs tutorial. Use the convenience keys already on your keyboard for navigation.
*   Learn a minimal subset of emacs-specific keyboard shortcuts.
*   Learn your language.
*   Watch a master do something

    *   Get annoyed that you don't know how to do that
    *   Learn how
    *   Repeat