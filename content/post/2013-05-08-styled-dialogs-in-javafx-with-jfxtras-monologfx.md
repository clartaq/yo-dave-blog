---
title: Styled Dialogs in JavaFX with JFXtras MonologFX
tags:
  - javafx
id: 587
comment: false
categories:
  - programming
date: 2013-05-08 14:14:30
modified: 2018-03-16T11:04:56.135457-04:00
---

I've been having a lot of fun learning [JavaFX](http://www.oracle.com/technetwork/java/javase/overview/javafx-overview-2158620.html "Link to JavaFX developer"), even after many years of using the Java Swing framework.

One thing about JavaFX that I still don't understand is the lack of built-in support for dialogs. Well, that shortcoming has annoyed enough people that there are several efforts underway to provide dialog functionality. One of those is part of the [JFXtras](http://jfxtras.org/ "Link to the JFXtras home page.") project, [MonologFX](https://blogs.oracle.com/javajungle/entry/monologfx_floss_javafx_dialogs_for "Link to MonologFX introduction blog.").

<!--more-->I had been experimenting with some of the capabilities for styling a JavaFX interface, including flat buttons as in Windows 8\. Flat interfaces seem to be all the rage now. MonologFX includes the ability to set a CSS style sheet to use for the dialogs it creates _via_ the `addStyleSheet()` method.

However, when I tried to specify the same style sheet I was using for the rest of the project, I kept getting errors about not finding the resource.

After some exchanges with the package author, the very helpful Mark Heckler, I finally understood that the style sheet had to reside in a directory within the source for the MonologFX package itself -- you could add the style sheet and rebuild the package and things would work as expected. Well, that didn't seem to be very useful. Who wants to rebuild the dialog package with a new style sheet for every different style in every different project?

Then it finally occurred to me. The important thing is the path to the resource. All you need to do is add a path to the resource in your program that mirrors the path to the resources in the MonologFX package. In this case that is `src\jfxtras\labs\dialogs`. With that directory in my project and my style sheet in that directory, things work as expected. For example, here's a screen capture of a tic-tac-toe program.

[![Styled Tic-Tac-Toe Application with JavaFX and MonologFX](https://github.com/clartaq/yo-dave/raw/master/images/2013-05-08-DialogSnip.png)<br><small>Styled Tic-Tac-Toe Application with JavaFX and MonologFX</small>](https://github.com/clartaq/yo-dave/raw/master/images/2013-05-08-DialogSnip.png)

The lower button, labeled "Reset" is styled and created in the main program. The dialog is created and styled with MonologFX.

The only annoyance now is having the same style sheet in two places: the main project directory and the MonologFX directory. It's easy to get them out of sync. There is probably a way to get [Leiningen](https://github.com/technomancy/leiningen "Link to the Leiningen home page"), the build tool I use for [Clojure](http://clojure.org/ "Link to the Clojure language home page.") programs, to copy the one in the main directory to the MonologFX directory. Just have to find it.