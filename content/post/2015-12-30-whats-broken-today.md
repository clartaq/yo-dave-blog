---
title: What's Broken Today
date: 2015-12-30
tags:
  - configuration
  - upgrading
---

Like most days, I got up ready to tackle a bunch of problems and features on some of the software I work on. But, as is often the case, I was stymied by technical problems and got almost nothing done. The problems?

- None of the [JetBrains IDEs](https://www.jetbrains.com/) that I use could open a working terminal window. For most work, these are my tools of choice for programming tasks. Without a terminal, I can get by, but it isn’t as convenient or fast. This is related to my recent upgrade to Windows 10. It was fixed once, but is broken again because of a new build of Windows 10.

- One of my servers had just stopped working without any indication as to why. Nothing unusual in the logs. Just had to start it back up. Now it’s acting like nothing ever happened at all.

- [Emacs](https://www.gnu.org/software/emacs/) will no longer launch a [CIDER repl](https://github.com/clojure-emacs/cider). Emacs is another of the editors I use for development. You can do just about anything with it — eventually. CIDER lets me run a Clojure REPL in emacs. Except today.

- [TortoiseHg](http://tortoisehg.bitbucket.io/) no longer shows file status icon overlays in Explorer. I use TortoiseHg as a Windows shell extension for the [Mercurial](https://www.mercurial-scm.org/) distributed version control system. It lets me do quick checks to assure myself that files have been changed, committed, *etc*. as expected. Now I’m back to doing it by hand. (In my opinion, NetBeans has the best integration with Mercurial. Unfortunately, the project in question was not in a language that NetBeans can work with. Must be one of the two or three left. ;-)

- I wanted to try something new and have one of my server programs show some pretty-printed Clojure maps in the browser. I’ve done pretty-printing in Clojure before. This time it was [ClojureScript](https://github.com/clojure/clojurescript). Alas, nothing worked. The examples I was able to find didn’t even compile. The official documentation seemed to be talking about a completely different piece of software. My IDE was able to find and display online documentation for the functions I was using, but the compiler said the namespace didn’t even exist.

So I spent hours tracking down the causes of these failures (and very few fixes.) It just makes me sad that we have to waste so much time on stuff like this.
