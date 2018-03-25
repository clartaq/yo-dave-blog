---
title: 'Java-Clojure Interop: An Update'
tags:
  - clojure
  - interop
  - java
id: 665
comment: false
categories:
  - programming
date: 2013-06-25 18:16:04
modified: 2018-03-16T16:42:36.332822-04:00
---

My most popular answer on [Stack Overflow](http://stackoverflow.com/) has to do with [Clojure-Java interop](http://stackoverflow.com/questions/2181774/calling-clojure-from-java/2187427#2187427). Since that answer was written, some of the tools used in the answer, specifically [enclojure](http://enclojure.wikispaces.com/), have been deprecated. Because many of the follow-up questions related to how to build a working version of the answer, I thought it might be a good idea to update the post with modern tools.

As this is written, the tools used include:

*   [Clojure](http://clojure.org/) 1.5.1
*   [Leiningen](https://github.com/technomancy/leiningen) 2.1.3
*   [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 1.7.0 Update 25

These instructions are for Windows 7 64-bit. Instructions for other OS's are similar.

# The Clojure Part #

First create a project and associated directory structure using Leiningen:

```dos
    C:\projects>lein new com.domain.tiny
```
Now, change to the project directory.

```dos
    C:\projects>cd com.domain.tiny
```
In the project directory, open the project.clj file and edit it such that the contents are as shown below.

```clojure
    (defproject com.domain.tiny "0.1.0-SNAPSHOT"
      :description "An example of stand alone Clojure-Java interop"
      :url "http://clarkonium.net/2013/06/java-clojure-interop-an-update"
      :license {:name "Eclipse Public License"
      :url "http://www.eclipse.org/legal/epl-v10.html"}
      :dependencies [[org.clojure/clojure "1.5.1"]]
      :aot :all
      :main com.domain.tiny)
```

Now, make sure all of the dependencies (Clojure) are available.

```dos
    C:\projects\com.domain.tiny>lein deps
```
You may see a message about downloading the Clojure jar at this point.

Now edit the Clojure file `C:\projects\com.domain.tiny\src\com\domain\tiny.clj` such that it contains the following contents. (This file was created when Leiningen created the project.)

```clojure
    (ns com.domain.tiny
      (:gen-class
        :name com.domain.tiny
        :methods [#^{:static true} [binomial [int int] double]]))
    
    (defn binomial
      "Calculate the binomial coefficient."
      [n k]
      (let [a (inc n)]
        (loop [b 1
               c 1]
          (if (> b k)
            c
            (recur (inc b) (* (/ (- a b) b) c))))))
    
    (defn -binomial
      "A Java-callable wrapper around the 'binomial' function."
      [n k]
      (binomial n k))

    (defn -main []
      (println (str "(binomial 5 3): " (binomial 5 3)))
      (println (str "(binomial 10042 111): " (binomial 10042 111))))
```

Much of the magic here is in the namespace declaration. The `:gen-class` tells the system to create a class named `com.domain.tiny` with a single static method called `binomial`, a function taking two integer arguments and returning a double. There are two similarly named functions `binomial`, a traditional Clojure function, and `-binomial` and wrapper accessible from Java. Note the hyphen in the function name `-binomial`. The default prefix is a hyphen, but it can be changed to something else if desired. The` -main` function just makes a couple of calls to the binomial function to assure that we are getting the correct results. To do that, compile the class and run the program.

```dos
    C:\projects\com.domain.tiny>lein run
```

You should see output similar to the following:

```dos
    Compiling com.domain.tiny
     (binomial 5 3): 10
     (binomial 10042 111): 4906838957506814494...
```
Now package it up in a jar and put it someplace convenient. Copy the Clojure jar there too.

```dos
    C:\projects\com.domain.tiny>lein jar
    Created C:\projects\com.domain.tiny\target\com.domain.tiny-0.1.0-SNAPSHOT.jar
    C:\projects\com.domain.tiny>mkdir \target\lib
    C:\projects\com.domain.tiny>copy target\com.domain.tiny-0.1.0-SNAPSHOT.jar target\lib\
            1 file(s) copied.
    C:\projects\com.domain.tiny>copy "C:<path to clojure jar>\clojure-1.5.1.jar" target\lib\
            1 file(s) copied.
```

# The Java Part #

Leiningen has a built-in task, `lein-javac`, that should be able to help with the Java compilation. Unfortunately, it seems to be broken in version 2.1.3\. It can't find the installed JDK and it can't find the Maven repository. The paths to both have embedded spaces on my system. I assume that is the problem. Any Java IDE could handle the compilation and packaging too. But for this post, we're going old school and doing it at the command line.

First create the file `Main.java` with the following contents.

```java
    import com.domain.tiny;
    
    public class Main {
        public static void main(String[] args) {
            System.out.println("(binomial 5 3): " + tiny.binomial(5, 3));
            System.out.println("(binomial 10042, 111): " + tiny.binomial(10042, 111));
        }
    }
```
To compile java part

```dos
    javac -g -cp target\com.domain.tiny-0.1.0-SNAPSHOT.jar -d target\src\com\domain\Main.java
```

Now create a file with some meta-information to add to the jar we want to build. In `Manifest.txt`, add the following text

```dos
    Class-Path: lib\com.domain.tiny-0.1.0-SNAPSHOT.jar lib\clojure-1.5.1.jar
    Main-Class: Main
```

Now package it all up into one big jar file.

```dos
    C:\projects\com.domain.tiny\target>jar cfm Interop.jar Manifest.txt Main.class lib\com.domain.tiny-0.1.0-SNAPSHOT.jar lib\clojure-1.5.1.jar
```

To run the program:

```dos
    C:\projects\com.domain.tiny\target>java -jar Interop.jar
    (binomial 5 3): 10.0
    (binomial 10042, 111): 4.9068389575068143E263
```
The output is essentially identical to that produced by Clojure alone, but the result has been converted to a Java double.

As mentioned, a Java IDE will probably take care of the messy compilation arguments and the packaging.