---
title: Loading Fonts Like CSS
date: 2013-08-10
tags:
  - clojure
  - javafx
categories:
  - programming
---

Just wanted to pass along a little snippet I have found myself using fairly frequently. CSS has the ability to specify the appropriate font to use in displaying a document. It handles the tag in such a way that it can gracefully degrade from a "preferred" font through a series of less ideal typefaces depending on what's available on the machine doing the display.

That's a handy facility to have, even on Windows, which can have different fonts available depending on the version of Windows and what software has been installed.

Here's a little program that illustrates the technique.

```clojure
   (ns demo.core
      (:gen-class extends javafx.application.Application)
      (:import
       [javafx.application Application]
       [javafx.event EventHandler]
       [javafx.scene Scene]
       [javafx.scene.control Button]
       [javafx.scene.layout StackPane]
       [javafx.scene.text Font]))
    
    (defn- get-pref
      "Return the first name from prefs that appears in the
       list of font families."
      [prefs families]
      (first (reverse (reduce (fn [result item]
                                (if (.contains families item)
                                  (cons item result)
                                  result))
                              () prefs))))
    
    (defn- pref-typeface
      "Return the typeface to be used at run time. Select the
       typeface from a group of pre-defined font families."
      []
      (let [prefs '("Fantasy" "Not Real" "Segoe UI" "Verdana"
                    "Trebuchet MS" "Tahoma" "Arial" "Helvetica"
                    "sans-serif" "System")
            families (Font/getFamilies)]
        (get-pref prefs families)))
    
    (defn- create-button-ext [txt is-default is-cancel]
      (let [btn (Button. txt)]
        (cond
          is-default (.setDefaultButton btn true)
          is-cancel (.setCancelButton btn true))
        btn))
    
    (defn- button-click-handler
      "Handle a click on the button. Change the text of the button to
       show the font name in the selected font."
      [btn fnt]
      (reify EventHandler
        (handle [this event]
          (doto btn
            (.setText (str "Hi, I'm " (.getName fnt)))
            (.setFont fnt)))))
    
    (defn -start
      "Build the application interface and start it up."
      [this stage]
      (let [root (StackPane.)
            scene (Scene. root 600 400)
            fnt (Font. (pref-typeface) 16)
            go-btn (create-button-ext "Press Me!" true false)]
    
        (.setOnAction go-btn (button-click-handler go-btn fnt))
    
        (.add (.getChildren root) go-btn)
    
        (doto stage
          (.setTitle "Font Loading Demo")
          (.setScene scene)
          (.show))))
    
    (defn -main
      [& args]
      (Application/launch demo.core args))
```

And here's a Leiningen script to build the program.

```clojure
    (defproject demo "0.1.0-SNAPSHOT"
      :description "Demo of loading JavaFX fonts in Clojure."
      :url "http://example.com/FIXME"
      :license {:name "Eclipse Public License"
                :url "http://www.eclipse.org/legal/epl-v10.html"}
      :dependencies [[org.clojure/clojure "1.5.1"]]
      :resource-paths ["c:\\Program Files\\Java\\jdk1.7.0_25\\jre\\lib\\jfxrt.jar"]
      :aot :all
      :main demo.core)
```

(I will certainly be glad when Java 8 and JavaFX 8 are out so I won't have to go through this stuff of having to specify where the JavaFX run-time is located.)

The interesting stuff is in the `pref-typeface` and `get-pref` functions. In `pref-typeface`, you supply a list of potential fonts to use, listing the most preferred first and the least preferred last. ("Fantasy" and "Not Real" are not real typefaces. They are only to show that things work when the most preferred fonts are not available.)

The `get-pref` function is a bit more general purpose. It finds and returns the first element of `prefs` that appears in the list of available font families.

So you end up using the best available font on the system you are running.

I have not tried this on Linux or Mac, so don't know how universally applicable it is.