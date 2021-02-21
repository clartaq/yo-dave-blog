---
title: Getting enclojure 1.4 to work
date: 2011-01-27 01:31:33
modified: 2018-03-15T10:26:32.505529-04:00
tags:
  - clojure
  - editors
  - enclojure
id: 40
comment: false
categories:
  - programming
---

I've used <del>[enclojure](http://www.enclojure.org)</del> (**Broken Link**) for a long time (in internet years). It has always seemed a bit finicky. However, with the 1.4 release and the switch to using [Maven](http://maven.apache.org) as the build tool, things stopped working. Projects that had worked fine before no longer compiled or executed. The "Getting Started" section of the enclojure web page appears to be hopelessly out of date and actually misleading. Here's what I had to do.

<!--more-->

## **Building and Running**

System: NetBeans 6.9.1, enclojure 1.4, Apache Maven 2.2.1, Java jdk 1.6.0_23, Encoding Cp1252, Windows XP, 32-bit or Windows 7, 64-bit

*   Create a new clojure 1.2 project.
*   Paste the source of a canonical Clojure program. I like this program because it uses Clojure, has interaction with Java, uses a macro, and generates a runnable jar using AOT compilation -- or _should_.
```clojure
    (ns net.dneclark.JFrameDemo
      (:import (javax.swing JLabel JButton JPanel JFrame))
      (:gen-class))

    ;;; This is a small app to demonstrate how to create
    ;;; a swing-based user interface from Clojure. It is
    ;;; stolen almost completely from Stuart Sierra's
    ;;; post at stuartsierra.com/2010/01/03/doto-swing-with-clojure
    
    (defmacro on-action [component event &amp;amp; body]
      `(. ~component addActionListener
          (proxy [java.awt.event.ActionListener] []
            (actionPerformed [~event] ~@body))))

    (defn counter-app []
      (let [counter (atom 0)
            label (JLabel. "Counter: 0")
            button (doto (JButton. "Add 1")
                     (on-action evnt  ;; evnt is not used
                       (.setText label
                          (str "Counter: " (swap! counter inc)))))
            panel (doto (JPanel.)
                    (.add label)
                    (.add button))]
        (doto (JFrame. "Counter App")
          (.setContentPane panel)
          (.setDefaultCloseOperation JFrame/EXIT_ON_CLOSE)
          (.setSize 300 100)
          (.setVisible true))))
    
    (defn -main []
      (counter-app))
```

*   In the project view, right-click on the project name, then select "Properties" from the drop-down menu.
*   In the "Run" branch, in the "Main Class:" edit box, type "net.dneclark.JFrameDemo".
*   Edited project pom.xml to include 2.0.2 under the maven compiler plugin as suggested by [this thread](http://groups.google.com/group/enclojure/browse_thread/thread/785bde127d006b8a/a5446ddf6e869dea?#a5446ddf6e869dea) from the enclojure discussion group.
For one of my systems, this is all it took. On another system, there was something funky in the Maven repository. Deleting the repository and re-building got things working.

## Other Tricks

At this point, the executable jar is called "sample-0.0.1.jar", to rename it, open the project properties, change the Artifact Id to "JFrameDemo". Executable jar is named "JFrameDemo-0.0.1.jar".

This jar is not a stand-alone executable as it is typically created by NetBeans. It does not contain any of the libraries, such as the Clojure libraries.