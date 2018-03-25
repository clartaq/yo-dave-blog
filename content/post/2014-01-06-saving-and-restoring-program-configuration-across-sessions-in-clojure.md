---
title: Saving and Restoring Program Configuration across Sessions in Clojure
date: 2014-01-06 13:13:46
tags:
  - clojure
  - javafx
categories:
  - programming
---

I like to use programs that can remember what I was doing the last time I was working with them. They should restore the window just as I had it, remember which file(s) I was working with, what preferences I had selected, and so on. Naturally, I want the programs I write to be just as considerate of the user.

For some time, I've been fretting over the best way to do this in a Clojure program. Should I provide wrappers around the Java Preferences API? Some other mechanism? Turns out I should just embrace simplicity.

<!--more-->

Simple configuration data usually consists of key/value pairs. In Clojure, the natural data structure to use is the map. For example, the size and position of a program window might be encoded as something like this:
```clojure
{:x-pos 364.0, :y-pos 341.0, :width 440.0, :height 330.0}
```

The structure of the values can actually be arbitrarily complex. They can hold just about anything.

It turns out that Clojure has facilities built in that can store and retrieve such information from disk files easily. The following is a demonstration of how saving and restoring such information can be done. First, here's a project file:

```clojure
(defproject cnfdemo "0.1.0-SNAPSHOT"
  :description "Demonstration of saving/restoring configuration info in Clojure"
  :url "https:/bitbucket.org/David_Clark/cnfdemo/overview"
  :license {:name "Eclipse Public License"
            :url "http://www.eclipse.org/legal/epl-v10.html"}
  :dependencies [[org.clojure/clojure "1.5.1"]]
  :resource-paths ["c:\\Program Files\\Java\\jdk1.7.0_45\\jre\\lib\\jfxrt.jar"]
  :aot :all
  :main cnfdemo.core)
```

Nothing tricky here except making sure that the <code>:resource-paths</code> point to the location of the JavaFX runtime jar.

The progam itself:

```clojure
(ns cnfdemo.core
  (:gen-class
   :extends javafx.application.Application)
  (:require [clojure.edn :as edn])
  (:import
   [ java.awt Dimension Toolkit]
   [ javafx.application Application Platform]
   [ javafx.event EventHandler]
   [ javafx.scene Scene]
   [ javafx.scene.control TextArea]
   [ javafx.scene.layout StackPane VBox]))

(def default-width 300)
(def default-height 150)

(defn default-config
  "Create a reasonable default configuration. To be used on the
   first execution of the program or if any error occurs when
   trying to restore an existing configuration."
  []
  (let [tk (. Toolkit getDefaultToolkit)
        screensize (Dimension. (.getScreenSize tk))]
    {:x-pos (/ (- (.getWidth screensize) default-width) 2)
     :y-pos (/ (- (.getHeight screensize) default-height) 2)
     :width default-width
     :height default-height})

(defn read-config
  "Read configuration data back from disk"
  []
  (try
    (edn/read-string (slurp "config.clj"))
    (catch Exception e (default-config)))

(defn write-config
  "Writes configuration data (actually _any_ map) to disk."
  [config]
  (spit "config.clj" config))

(defn update-config [stage]
  {:x-pos (.getX stage)
   :y-pos (.getY stage)
   :width (.getWidth stage)
   :height (.getHeight stage)})

(defn handle-close-request [stage]
  (reify EventHandler
    (handle [this event]
      (let [config (update-config stage)]
        (write-config config)))))

(defn restore-config [stage config]
  (doto stage
    (.setX (:x-pos config))
    (.setY (:y-pos config))
    (.setWidth (:width config))
    (.setHeight (:height config))))

(defn handle-show-request [stage]
  (reify EventHandler
    (handle [this event]
      (let [config (read-config)]
        (restore-config stage config)))))

(defn -start
  "Build the application interface and start it up."
  [this stage]
  (let [root (StackPane.)
        ta (TextArea. "Saving and Restoring configuration data in Clojure")
        vb (VBox.)
        scene (Scene. root)]

    (.add (.getChildren vb) ta)
    (.add (.getChildren root) vb)

    (doto stage
      (.setOnShowing (handle-show-request stage))
      (.setOnCloseRequest (handle-close-request stage))
      (.setTitle "Config Demo")
      (.setScene scene)
      (.show))))

(defn -main [& args]
  (Application/launch cnfdemo.core args))
```

The demo works by linking into the program start-up and shutdown processes. Since this is a JavaFX program, the hooks can be inserted with the `.setOnShowing` and `.setOnCloseRequest` methods of the stage object. The `handle-show-request` and `handle-close-request` functions return `EventHandler`s for these two notifications respectively.

The operation of the program is straightforward. On closing the program, the relevant configuration information is updated by the `update-config` function and written to a file in the project directory, `config.clj`, by the `write-config` function.

When the program starts, the `config.clj` file is read by the `read-config` function and the configuration information is restored before the program window is displayed. There are two things to note about the `read-config` function. First, on the very first execution of the program, there is no configuration file on disk. In that case the program throws and catches an exception. When the exception is caught, the `default-config` function is called to create a reasonable group of default settings that the program should use.

Note that the program can still crash if the configuration file is present but contains bogus values, like an x position of "bogus", example.

The second interesting thing about read-config is that it uses `clojure.edn/read-string` instead of `clojure.core/read-string`. Since in Clojure, like all Lisps, program code is just data, and data can be program code, the use of the `clojure.core/read-string` could allow execution of arbitrary code from a malicious configuration file. This is true even if the Clojure global variable `*read-eval*` is set to false, since earlier versions of Clojure could still be tricked into running Java constructors through this path. See the discussion of `clojure.core/read` [here](http://clojuredocs.org/clojure_core/clojure.core/read).

Granted, it is pretty unlikely that a user would sabotage their own machine this way for this use case, but why not get into the habit of using `clojure.edn/read-string` anyway? It is a drop-in replacement for `clojure.core/readstring` for this purpose.

If you would like to try this yourself, you can download a project repository from [here](https://bitbucket.org/David_Clark/cnfdemo/overview).