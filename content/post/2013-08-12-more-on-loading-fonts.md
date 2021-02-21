---
title: More on Loading Fonts
date: 2013-08-12
modified: 2018-03-27T10:05:47.591726-04:00
tags:
  - clojure
  - javafx
categories:
  - programming
---

A couple days ago, I [posted a little snippet](https://yo-dave.com/2013/08/10/loading-fonts-like-css/) showing how to load and font from a list of preferred fonts using [Clojure](http://clojure.org/) and [JavaFX](http://www.oracle.com/technetwork/java/javafx/overview/index.html). Well, I've extended the demo a bit to show how to load both fonts installed on the OS *and* fonts from a resource file.

Here's the new snippet.

```clojure
    (ns clojure_font_loading.core
      (:gen-class
       :extends javafx.application.Application)
      (:import
       [javafx.application Application]
       [javafx.event EventHandler]
       [javafx.scene Scene]
       [avafx.scene.control Button]
       [javafx.scene.layout StackPane  VBox]
       [javafx.scene.text Font])
      (:require [clojure.java.io :as jio]))
    
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
    
    (defn- get-font-from-resource
      "Load the named font from a resource."
      [font-name]
      (let [prefix "clojure_font_loading/resources/"
            url (jio/resource (str prefix font-name))
            fnt (Font/loadFont (.toExternalForm url) 20.0)]
        fnt))
    
    (defn -start
      "Build the application interface and start it up."
      [this stage]
      (let [root (StackPane.)
            vbox (VBox.)
            scene (Scene. root 600 400)
            fnt1 (Font. (pref-typeface) 16)
            fnt2 (get-font-from-resource "ITCBLKAD.TTF")
            btn1 (Button. "Click to load Installed font")
            btn2 (Button. "Click to load from Resource")]
    
        (.setOnAction btn1
                     (reify EventHandler
                       (handle [this event]
                         (doto btn1
                           (.setText (str "This is " (.getName fnt1)
                                          " loaded from an installed font"))
                           (.setFont fnt1)))))
    
         (.setOnAction btn2
                      (reify EventHandler
                        (handle [this event]
                          (doto btn2
                            (.setText (str "This is " (.getName fnt2)
                                           " loaded from a font file"))
                            (.setFont fnt2)))))
    
        (.setSpacing vbox 10.0)
    
        (doto (.getChildren vbox)
          (.add btn1)
          (.add btn2))
    
        (.add (.getChildren root) vbox)
    
        (doto stage
          (.setTitle "Font Loading Demo")
          (.setScene scene)
          (.show))))
    
    (defn -main
      [& args]
      (Application/launch clojure_font_loading.core args))
```

Here's the project file.

```clojure
    (defproject clojure_font_loading "0.1.0-SNAPSHOT"
      :description "Demonstration of loading fonts in Clojure and JavaFX"
      :url "https://bitbucket.org/David_Clark/clojure-font-loading"
      :license {:name "Eclipse Public License"
                :url "http://www.eclipse.org/legal/epl-v10.html"}
      :dependencies [[org.clojure/clojure "1.5.1"]]
      :resource-paths ["c:\\Program Files\\Java\\jdk1.7.0_25\\jre\\lib\\jfxrt.jar"]
      :aot [clojure_font_loading.core]
      :main clojure_font_loading.core)
```

I've put this in a [repository](https://helixteamhub.cloud/Regolith/projects/binom-stats/repositories/clojure-font-loading/tree/default) at HelixTeamHub if you're interested. Note that you will have to create a sub-directory off of the `src/clojure_font_loading/` directory called `resources`. Then copy a font file of your choice into that directory. Then you will need to change the name of the font file in the source. (I did this because of fears of putting someone's copyrighted material into the repository. Don't want to do that.)