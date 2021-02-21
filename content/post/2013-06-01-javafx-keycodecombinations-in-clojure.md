---
title: JavaFX KeyCodeCombinations in Clojure
tags:
  - clojure
  - java
  - javafx
id: 638
comment: false
categories:
  - programming
date: 2013-06-01 22:26:04
modified: 2018-03-16T16:36:05.718895-04:00
---

I've been experimenting with adding keyboard accelerators to some of the [Clojure](http://clojure.org/) programs I've written with [JavaFX](http://www.oracle.com/technetwork/java/javafx/overview/index.html)-based user interfaces. As part of that investigation, I tried to translate the [Java ](http://en.wikipedia.org/wiki/Java_%28programming_language%29) program <del>[here](http://www.example8.com/category/view/id/76 "Link to Java version of program.")</del> (**Broken Link**) to Clojure. The program just puts up a window with a menu bar containing only a "`File`" menu which itself contains one item, "`Exit`". Most programs provide a keyboard shortcut or accelerator to close the program with a `Ctrl-X` (on Windows). Figuring out how to add that functionality was a bit of an issue for me.

<!--more-->It turns out that the constructor for a `KeyCodeCombination` accepts a variable length argument array. In a few cases, you can get lucky and simply provide a Clojure vector of the arguments. This wasn't one of those happy coincidences. Instead, you need to use the canonical method of packing all of those arguments into a Java array using Clojure's `into-array` function. You need to do it even if you have only one argument, as was the case for me. That isn't usually so bad, but this time I had a little trouble figuring out the type of array to create. Turns out you have to specify that the list of modifiers expected by the constructor are `KeyCombination.Modifier` arguments in Java are `KeyCombination$Modifier` in Clojure.

Here's the translated program:

```clojure
    (ns cljkeycodecombination.core
      (:gen-class
       :extends javafx.application.Application)
      (:import
       [ javafx.application Application Platform]
       [ javafx.event EventHandler]
       [ javafx.scene Scene]
       [ javafx.scene.control Menu MenuBar MenuItem]
       [ javafx.scene.input KeyCode KeyCodeCombination KeyCombination KeyCombination$Modifier]
       [ javafx.scene.layout BorderPane]
       [ javafx.scene.paint Color]))
    
    (defn -start [this stage]
      (let [root (BorderPane.)
            scene (Scene. root 400.0 300.0 Color/WHITE)
            menu-bar (MenuBar.)
            menu (Menu. "File")
            exit-item (MenuItem. "Exit")]
    
        (.setMnemonicParsing exit-item true)
        (.setAccelerator exit-item
                         (KeyCodeCombination.
                          KeyCode/X
                          into-array KeyCombination$Modifier [KeyCombination/SHORTCUT_DOWN])))
    
        (.setOnAction exit-item
                      (proxy [EventHandler] []
                        (handle [event]
                          (Platform/exit))))
    
        (.add (.getItems menu) exit-item)
        (.add (.getMenus menu-bar) menu)
        (.setTop root menu-bar)
    
        (doto stage
          (.setTitle "KeyCodeCombination Test")
          (.setScene scene)
          (.show))))
    
    (defn -main [& args]
      (Application/launch cljkeycodecombination.core args))
```

Looking back, it's a little embarrassing that it took so long to figure out since the clue was right there in the error messages. But at least it's figured out now.