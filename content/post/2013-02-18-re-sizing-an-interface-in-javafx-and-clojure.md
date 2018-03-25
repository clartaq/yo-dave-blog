---
title: Re-sizing an Interface in JavaFX and Clojure
tags:
  - clojure
  - java
  - javafx
  - swing
id: 566
comment: false
categories:
  - programming
date: 2013-02-18 21:48:11
modified: 2018-03-25T15:49:58.578662-04:00
---

Since [JavaFX](http://www.oracle.com/technetwork/java/javase/overview/javafx-overview-2158620.html "JavaFX overview") is the [future of the user interface for Java](http://www.oracle.com/technetwork/java/javafx/overview/faq-1446554.html#6 "JavaFX FAQ"), I've started trying to learn it. Since I'm also learning [Clojure](http://clojure.org/ "Clojure home page"), I'm doing the work in that language.

One of the things I've been looking into is how the interface responds to resizing. If you have all of your controls in a nice layout, that is usually taken care of for you. But how do you handle things if the interface is not made up of standard components, something like a graphical game interface for example?

Well, one of the tasks I've set for myself recently was the creation of an interface for the game of tic-tac-toe. Such an interface should look something like the following:

(**NOTE**: The original image and the original program have been lost. The following image is a reasonable facsimile.)

[![The startup screen for the tic-tac-toe application.](/static/img/2013-05-08-PlaySnip.png)<br><small>The startup screen for the tic-tac-toe application.</small>](/static/img/2013-05-08-PlaySnip.png) 

When the window is re-sized, all of the requirements listed above are met.

Here's the listing of the program that produces the interface shown above. There is no logic for actually playing the game, just re-sizing and re-drawing the board (with some hard-wired symbols).

```clojure
(ns titato.core
  (:gen-class
   :extends javafx.application.Application)
  (:import
   [ javafx.application Application ]
   [ javafx.beans.value ChangeListener ]
   [ javafx.scene Scene ]
   [ javafx.scene.canvas Canvas ]
   [ javafx.scene.layout Pane ]
   [ javafx.scene.paint Color ]))

;; define the board with some "hard-wired" symbols for demo only
(def board (hash-map 1 "1" 2 "2" 3 "o" 4 "4" 5 "x" 6 "6" 7 "7" 8 "o" 9 "9"))

(def window-width 400)
(def window-height 300)

(def o-color (Color. 0.75 1.0 0.75 1.0))
(def x-color (Color. 0.65 0.75 1.0 1.0))
(def board-color (Color. 1.0 1.0 0.8 1.0))
(def line-color (Color/SLATEGRAY))

(defn draw-o [canvas x y radius stroke-width]
  (let [gc (.getGraphicsContext2D canvas)]
    (doto gc
      (.setStroke o-color)
      (.setLineWidth stroke-width)
      (.strokeOval (- x radius) (- y radius) (+ 1 (* 2 radius)) (+ 1 (* 2 radius))))))

(defn draw-x [canvas x y radius stroke-width]
  (let [gc (.getGraphicsContext2D canvas)]
    (doto gc
      (.setStroke x-color)
      (.setLineWidth stroke-width)
      (.strokeLine (- x radius) (- y radius) (+ x radius) (+ y radius))
      (.strokeLine (- x radius) (+ y radius) (+ x radius) (- y radius)))))

(defn filled-spaces
  "Return a map of those board positions containing x's or o's."
  [board]
  (select-keys board (for [[k v] board :when (or (= v "o") ( = v "x"))] k)))

(defn board-to-col-row
  "Convert board position to zero-based column and row positions. Return
   a vector or the column and row. Valid input consists of the integers from
   1 to 0 inclusive."
  [pos]
  (let [pm1 (dec pos)
        col (mod pm1 3)
        row (quot pm1 3)]
    [col row]))

(defn redraw-board [canvas]
  (let [gc (.getGraphicsContext2D canvas)
        w (.getWidth canvas)
        h (.getHeight canvas)
        board-size (* 0.8 (min w h))
        cell-size (/ board-size 3)
        half-cell (/ board-size 6)
        sym-size (* 0.6 cell-size)
        sym-radius (/ sym-size 2)
        sym-stroke-width (* 0.15 cell-size)
        x-off (/ (- w board-size) 2)
        y-off (/ (- h board-size) 2)]

    ;; draw the game board
    (doto gc
      (.setFill board-color)
      (.fillRect 0 0 w h)
      (.setStroke line-color)
      (.setLineWidth 5)

      (.strokeLine x-off (+ y-off cell-size) (- w x-off) (+ y-off cell-size))
      (.strokeLine x-off (- h (+ y-off cell-size)) (- w x-off) (- h ( + y-off cell-size)))
      (.strokeLine (+ x-off cell-size) y-off (+ x-off cell-size) (- h y-off))
      (.strokeLine (- w (+ x-off cell-size)) y-off (- w (+ x-off cell-size)) (- h y-off)))

    ;; draw symbols from game board map
    (let [filled (filled-spaces board)]
      (doall
       (for [[pos sym] filled
             :let [[col row] (board-to-col-row pos)
                   x (+ x-off half-cell (* col cell-size))
                   y (+ y-off half-cell (* row cell-size))]]
           (if (= sym "x")
             (draw-x canvas x y sym-radius sym-stroke-width)
             (draw-o canvas x y sym-radius sym-stroke-width)))))))

(defn -start [this stage]
  (let [root (Pane.)
        scene (Scene. root window-width window-height)
        canvas (Canvas.)]

    (doto (.widthProperty canvas)
      (.bind (.widthProperty root))
      (.addListener
       (proxy [ChangeListener] []
         (changed [ov old-state new-state]
           (redraw-board canvas)))))
    (doto (.heightProperty canvas)
      (.bind (.heightProperty root))
      (.addListener
       (proxy [ChangeListener] []
         (changed [ov old-state new-state]
           (redraw-board canvas)))))

    (.add (.getChildren root) canvas)
    (doto stage
      (.setTitle "Tic Tac Toe")
      (.setScene scene)
      (.setMinHeight window-height)
      (.setMinWidth window-width)
      (.show))
    (redraw-board canvas)))

(defn -main [&amp; args]
  (Application/launch titato.core args))

```

And here's the project file.

```clojure
(defproject titato "0.1.0-SNAPSHOT"
  :description "Demo of a resizable JavaFX tic-tac-toe interface"
  :url "http://example.com/FIXME"
  :license {:name "Eclipse Public License"
            :url "http://www.eclipse.org/legal/epl-v10.html"}
  :dependencies [[org.clojure/clojure "1.4.0"]]
  :resource-paths ["c:\\Program Files\\Java\\jdk1.7.0_13\\jre\\lib\\jfxrt.jar"]
  :main titato.core)
```

Note the use of the `resource-path` in the project file. That's how [leiningen](https://github.com/technomancy/leiningen "Leiningen home page") finds JavaFX.

The magic begins in the `-start` function. That's the function in your program that JavaFX calls to start things up. The first thing required is that your root node is actually re-sizable. Hence the use of a `Pane`. See [Amy Fowler's blog](http://amyfowlersblog.wordpress.com/2011/06/02/javafx2-0-layout-a-class-tour/ "Amy Fowler") for a nice description of the layout classes. Many JavaFX examples use a `Group` object. That won't work here because it is not re-sizable.

The next important thing that happens in `-start` is to bind the width and height properties of the `Canvas` to the width and height properties of the root `Pane`. This was new to me. It isn't the same mechanism as is used in [Swing](http://en.wikipedia.org/wiki/Swing_%28Java%29 "Description of Java Swing on Wikipedia"). By binding the width and height properties, the canvas gets notified any time they change. The `ChangeListener`s added to the two properties get called when the properties change providing the opportunity to re-draw the interface. The re-draw occurs by calling the `redraw-board` function on the canvas whenever the size changes.

At first I thought that re-drawing the board on every change during a drag might be too slow. However, I can't seem to outrun the program no matter how fast I move the mouse. So, this method seems to work for a program with an interface as simple as this one.