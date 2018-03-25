---
title: A Titled JavaFX Separator
date: 2017-08-31 13:50:58
tags:
  - javafx
  - clojure
categories:
  - programming
---

In the process of updating some old programs, I had to change the GUI frameworks used. The old programs were written in Java using the Swing GUI framework and the [JGoodies](http://www.jgoodies.com/) [Forms](http://www.jgoodies.com/freeware/libraries/forms/) and [Looks](http://www.jgoodies.com/freeware/libraries/looks/) libraries. Nowadays, the official GUI framework for Java is JavaFX.

Making the transition from Swing to JavaFX was relatively painless because the programs were so small. However, one of the things I missed from the JGoodies Forms library was the "titled separator", that is a separator with a label in front of it. I really liked the look. Turns out that making my own wasn't that difficult. Here's a program extract that implements my version. (This time it's written in Clojure.)

```Clojure
(def sep-color "#4682b4")                                   ; steelblue
(def sep-style (str/join "" ["-fx-border-color: "
                             sep-color
                             "; -fx-border-width: 1 0 0 0 ;"]))

(defn special-separator
  "Return a special separator used for titled separators below. This
  separator is specially colored and set to fill as much space as
  available in the HBox where it is used."
  []
  (let [sep (Separator.)]
    (doto sep
      (.setStyle sep-style)
      (HBox/setHgrow Priority/ALWAYS))
    sep))

(defn titled-separator
  "Return a separator with a title preceding the separator line. If title
  is empty or nil, a separator consisting of only the line is returned."
  [title]
  (let [labeled-separator (HBox.)
        no-label (or (nil? title)
                     (zero? (count title)))
        label (if no-label
                (Label. "")
                (Label. title))]
    (when-not no-label
      (.setPadding label (Insets. 0.0 5.0 0.0 0.0))
      (.setStyle label "-fx-font-weight: bold;")
      (.setTextFill label (Color/web sep-color)))
    (.setAlignment labeled-separator Pos/CENTER)
    (.addAll (.getChildren labeled-separator) [label (special-separator)])
    labeled-separator))

```

Instead of the standard black separator, this one is a nice grayish-blue. It coordinates with lots of the color schemes I use but is easy to change. Using it is pretty easy too.  Just do something like:

```clojure
    (titled-separator "Here's a Title")
```
and back comes a separator with a textual title in front of the rule line. If you call the function without a title, it will just return a nicely colored separator. The label will be in a bold version of the font normally used by the program and will be separated from the rule line by a few pixels. You can see what they look like in this screen shot of the [sign test program that I udpated recently](https://clartaq.github.io/yo-dave/2017/08/31/2017-08031-an-updated-sign-test-program/).

[![A New Version of the Sign Test Program](https://github.com/clartaq/yo-dave/raw/master/images/2017-08-32-new-sign-test-program.PNG "A New Version of the Sign Test Program")<br><small>A New Version of the Sign Test Program</small>](https://github.com/clartaq/yo-dave/raw/master/images/2017-08-32-new-sign-test-program.PNG)

 
