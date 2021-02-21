---
title: Leiningen passing Invalid Flags to Java Compiler
tags:
  - programming
  - clojure
  - java
  - leiningen
date: 2017-05-14 09:54:13
---

Just a note about some weirdness in my work process and it&#8217;s solution.

A few weeks ago, I started noticing some weirdness in trying to use some tools with [Leiningen](https://leiningen.org/) while developing a program in [Clojure](https://clojure.org/). When running tools like [`kibit`](https://github.com/jonase/kibit), `lein` would fail with an error from `javac` about an invalid flag. Initially these flags were for attempts to set the file encoding. And the file encoding kept changing.

Over time, these failures became more frequent. Always about passing an invalid flag to the java compiler. Finally, I was unable to run a REPL, run a program, or build an uberjar.

Long story short, it looks like it was related to the `JAVA_CMD` environment variable. That had been set a loooong time ago and in fact pointed to the java compiler, not the VM. Simply removing the variable fixed things. Might have worked just by setting it to `java` rather than `javac` but I didn't try it and so far nothing else has broken.

Still don't know what caused things to get flaky in the first place since this variable was set so long ago.

**Update 2017-08-21**: Eventually, I found that I needed to set the `JAVA_CMD` environment variable again to get something working. (I forget what.) This time, I pointed it to `java.exe` rather than the compiler `javac.exe`, and everything works.