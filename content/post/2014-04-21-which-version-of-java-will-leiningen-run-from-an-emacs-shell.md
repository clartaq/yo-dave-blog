---
title: Which Version of Java will Leiningen Run from an Emacs Shell
date: 2014-04-21 13:51:52
tags:
  - clojure
  - emacs
  - java
  - leiningen
categories:
  - programming
---

I've been updating some of my projects to use the newly released Java 8. That includes many Clojure projects. These are just "flow of consciousness" debugging notes.

<!--more-->

Under Linux, things work as expected. Things are not so straightforward under Windows.

At a command prompt, `lein version` produces the following output, as expected:

```shell
Leiningen 2.3.4 on Java 1.8.0 Java HotSpot(TM) 64-Bit Server VM
```

When I run `lein version` as an Emacs shell command, the output is:

```shell
Leiningen 2.3.4 on Java 1.7.0_40 Java HotSpot(TM) Client VM
```

Running in a Clojure REPL under Emacs produces the similar:

```clojure
;nREPL 0.1.8-preview
user> (System/getProperty "java.version")
"1.7.0_40"
```

What's going on?

The Leiningen sample project file at [https://github.com/technomancy/leiningen/blob/master/sample.project.clj](https://github.com/technomancy/leiningen/blob/master/sample.project.clj) says that Leiningen's own JVM is set with the `LEIN_JAVA_CMD` environment variable. In my case, no such variable is set. As I interpret the `lein.bat` file, that means that the java used defaults to simple "java". I would think that means the same Java used by the Windows command prompt. Apparently not.

Creating the Windows environment variable `LEIN_JAVA_CMD` and setting it to `C:\Program Files\Java\jdk1.8.0\bin\java` gets the desired effect when we ask Leiningen for its version number in an Emacs shell command.

However, things are even weirder at the REPL.

```clojure
; nREPL 0.1.8-preview
user> (System/getProperty "java.version")
"1.7.0_25"
```

Where did that come from?

I have JDK versions 1.7.0_17, _21, _25, _40, _45, _51, and 1.8.0 installed on my system. How are these choices being made?

Tried updating to [CIDER](https://github.com/clojure-emacs/cider). Same results.

```clojure
; CIDER 0.6.0alpha (package: 20140322.332) (Clojure 1.6.0, nREPL 0.2.3)
eight-queens.core> (System/getProperty "java.version")
"1.7.0_25"
```

Running LightTable (version 0.6.5) and starting an InstaRepl also produces the same result

```clojure
;; Anything you type in here will be executed
;; immediately with the results shown on the
;; right.
(System/getProperty "java.version")
```

Running the Clojure REPL directly from the command line give the expected result:

```clojure
C:\Users\david\.m2\repository\org\clojure\clojure\1.5.1>java -cp clojure-1.5.1.jar clojure.main
Clojure 1.5.1
user=> (System/getProperty "java.version")
"1.8.0"
```

Turns out there is a line in the user profile, `C:\Users\david\.lein\users.clj`, that seems to be messing things up.

```clojure
  :java-cmd "C:\\Program Files\\Java\\jdk1.7.0_25\\bin\\java.exe"
```

Not sure why that was in there. Removing that line also lets me remove the environment variable and the results from the emacs shell gives the correct result. However, the REPLs report version 1.7.0_40.

Giving up, I remove versions _17, _21, _25, _40, and _45 (leaving _51 in place). Now everything works as desired, all REPLs and shell commands reporting the correct version. Sheesh.
