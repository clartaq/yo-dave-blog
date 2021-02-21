---
title: A Closure in Clojure
tags:
  - clojure
  - closures
id: 80
comment: false
categories:
  - Programming
date: 2011-01-29 23:53:30
modified: 2018-03-15T10:30:36.828066-04:00
---

Back when closures were first explained to me, a long time ago, I thought "sounds like a language with pass-by-reference semantics like Pascal." Of course, it isn't quite that simple.

Clojure has a lot of nice features that work naturally to give you a "better Java than Java". Here's an example of using a closure that is not at all easy in Java.

<!--more-->

```clojure
(ns net.dneclark.JFrameAndTimerDemo
  (:import (javax.swing JLabel JButton JPanel JFrame Timer))
  (:gen-class))

(defn timer-action [label counter]
   (proxy [java.awt.event.ActionListener] []
     (actionPerformed
      [e]
       (.setText label (str "Counter: " (swap! counter inc))))))

(defn timer-fn []
  (let [counter (atom 0)
        label (JLabel. "Counter: 0")
        timer (Timer. 1000 (timer-action label counter))
        panel (doto (JPanel.)
                (.add label))]
    (.start timer)
    (doto (JFrame. "Timer App")
      (.setContentPane panel)
      (.setDefaultCloseOperation JFrame/EXIT_ON_CLOSE)
      (.setLocation 300 300)
      (.setSize 200 200)
      (.setVisible true)))
  (println "exit timer-fn"))

(defn -main []
  (timer-fn))
```

If you compile and run the program, you will see a small window with a counter than increments every second. You will also see a message displayed that the function `timer-fn` has exited. Big deal, huh?

But look at the declaration of the counter. It's in the definition of the `timer-fn`. It isn't a global. But the action listener, `timer-action`, still has access to the variable and continues to increment it -- even though the function that declared the variable has completed execution. The lexical context of that variable is maintained and the action listener has access to it. Very neat.

I don't know if Java will ever get closures, but it sure is simple to use them in Clojure.