---
author: david
title: Autosave Functionality in ClojureScript
date: 2018-08-01T15:56:32.168-04:00
modified: 2018-08-07T10:56:03.744-04:00
tags:
  - ClojureScript
  - concurrency
---


Most programs provide an "Autosave" feature these days. The feature gives the user a chance to minimize the amount of work lost due to some unforeseen mishap, like a sudden power outage.

When I write a program in Clojure that requires such a feature, I have the entire Java library at my disposal. I usually create something based on a [`ScheduledExecutorService`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ScheduledExecutorService.html) -- something that watches for a change in the document then allows a certain amount of user inactivity to pass before automatically saving the document. If the user makes another revision before the executor is triggered, the executor timer is reset to trigger after another period of inactivity.

When programming in ClojureScript, those concurrency facilities​ are not available. Instead, I came up with something like the following.

```clojure
(def delay-handle (atom nil))

(defn clear-autosave-delay! []
  (.clearTimeout js/window @delay-handle))

(defn start-autosave-delay!
  [doc-save-fn delay-ms page-map-atom]
  (reset! delay-handle (.setTimeout js/window doc-save-fn delay-ms page-map-atom)))

(defn change-watcher!
  [doc-save-fn page-map-atom]
  (let [delay (* 1000 (get-in @page-map-atom [:options :editor_autosave_interval]))]
    (when (pos? delay)
      (clear-autosave-delay!)
      (start-autosave-delay! doc-save-fn delay page-map-atom))))
```

I put the `change-watcher!` function in the `:on-ch​ange` handler for a control (or multiple controls). Whenever a change occurs, it resets the delay. If the delay expires, the `doc-save-fn` is called to do the save.

It isn't very "Clojuresque,"​ but it does seem to work ok.

Trying to find a more idiomatic solution, I posted [this question](https://stackoverflow.com/questions/51661320/autosave-functionality-in-clojurescript) on StackOverflow. It generated very little interest, but the single commenter did say that this seems to be a common practice -- putting a thin wrapper around some ugly JavaScript.​