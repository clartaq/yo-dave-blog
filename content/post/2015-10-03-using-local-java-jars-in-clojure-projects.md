---
title: Using Local Java JARS in Clojure Projects
date: 2015-10-03
tags:
  - clojure
  - java
  - leiningen
categories:
  - programming
---

Recently, I've been working on a [Sudoku](https://en.wikipedia.org/wiki/Sudoku) game program. Part of the program provides a user with the ability to generate new puzzles of a particular difficulty. Generating a puzzle usually requires two puzzle solvers: one that solves puzzles (slowly) like a human would, the other that solves puzzles (very quickly) like a computer would.

Rather than write my own from scratch, for this part of the development, I wanted to use an existing implementation of the machine-like solver. After a little research (more on this some other time), I found one I liked a lot -- the [Kudoku solver written in Java](https://github.com/attractivechaos/plb/tree/master/sudoku) from [attractive chaos](https://attractivechaos.wordpress.com/2011/06/19/an-incomplete-review-of-sudoku-solver-implementations/).

But how does one use a local jar file in a Clojure Project? Read on...

<!--more-->

Usually when you pull in a dependency for a Clojure program, you would grab it from [Clojars](https://clojars.org/) or some other repository. But this time, I wanted to use a JAR that exists on my local machine and nowhere else that I know of.

After googling for awhile, I found a lot of misleading (or perhaps outdated) stuff that just didn't work. So, this post describes what I did that finally got things working.

Before we start, here's a description of the system I'm using to make all this happen.

* Window 7 Pro, 64-bit
* Java 1.8.0 update 60, 64-bit
* Clojure 1.7.0
* Leiningen 2.5.2
* Maven 3.3.3

So, after making some simple changes to the Kudoku solver (benchmarking tests, simple API, trivial refactorings), I compiled it to a JAR file and gave it a version number in the same form as expected by Leiningen, e.g. `kudoku-2.0.2.jar`.

In my main project folder, I created a sub-directory to hold the JAR.

```batchfile
C:\projects\sudoku>mkdir local-repo
```

After copying the JAR into the new directory, the next step was to deploy the JAR to the local Maven repository.

```batchfile
mvn deploy:deploy-file -DgroupId=local -DartifactId=kudoku -Dversion=2.0.2 -Dpackaging=jar -Dfile=kudoku-2.0.2.jar -Durl=file:local-repo
```

This is the step that was most confusing as there is a lot of advice on the web to use `file:file-install` rather than `deploy:deploy-file`. That didn't work for my setup for some reason.

Next, some modifications to `project.clj`. Here's what I'm using.

```clojure
(defproject sudoku "0.2.10-SNAPSHOT"
  :description "A sudoku game written in Clojure and JavaFX."
  :url "https:/bitbucket.org/David_Clark/sudoku/overview"
  :license {:name "Eclipse Public License"
            :url "http://www.ecllein.org/legal/epl-v10.html"}
  :dependencies [[org.clojure/clojure "1.7.0"]
                 [org.jfxtras/jfxtras-labs "2.2-r6-SNAPSHOT"]
                 [local/kudoku "2.0.2"]]
  :repositories [["java.net" "http://download.java.net/maven/2"]
                 ["sonatype" {:url "http://oss.sonatype.org/content/repositories/snapshots"
                              :checksum :fail
                              :update :never
                              :releases {:checksum :fail :update :never}}]
                 ["local" ~(str (.toURI (java.io.File. "local-repo")))]]
  :test-paths ["test"]
  :aot :all
  :main sudoku.core)
```

Note particularly the line in the `:resources` section:

```clojure
                 ["local" ~(str (.toURI (java.io.File. "local-repo")))]]
```

and the line in the `:dependencies` section:

```clojure
                 [local/kudoku "2.0.2"]]
```

With these changes in place, a simple

```batchfile
lein deps
```

put everything in the right place and the project could be built problem free.

**Update**: After completing this work, it came to my attention that there is now an even easier way to do this, all in Leiningen. Check out the accepted answer to [this question on Stack Overflow](http://stackoverflow.com/questions/19496263/how-do-i-use-checked-in-jars-with-leiningen/19498585#19498585).