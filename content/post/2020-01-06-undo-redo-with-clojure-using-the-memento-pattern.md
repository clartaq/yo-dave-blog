---
author: david
title: Undo/Redo with Clojure using the Memento Pattern
date: 2020-01-05T17:27:07.847-05:00
modified: 2020-01-06T17:53:09.503-05:00
tags:
  - Clojure
  - Memento
  - Pattern
  - Redo
  - Undo
---

Because of the ability to attach "watch" functions to Clojure `atom`s, it is very easy to create and use a simple undo/redo mechanism.

The approach is to create a stack that holds every change to the state of the atom, as detected by a watch function. To undo something you just pop the last state off the stack. You can repeat it until you recover the original state of the `atom` as it existed when you attached the watch function.

Redo is just a variation. You create another stack, the redo stack, where the current state is pushed just before popping the old state on the undo stack. Undo and redo operations can be interleaved until an additional action, not recorded on either stack, modifies the atom. At that point the redo stack is cleared.

This is an informal implementation of the [Memento Pattern](https://en.wikipedia.org/wiki/Memento_pattern).

So, how would this be implemented? Well, it just so happens I have one right here.

```clojure
(defprotocol UndoProtocol
  "A protocol track changes to an atom, allowing undo and redo! operations on that atom."
  (init [this] "Initialize the state of thing implementing this protocol.")
  (num-undos [this] "Return the number of states that can be undone.")
  (num-redos [this] "Return the number of states that can be redone.")
  (can-undo? [this] "Return true if a state change can be undone.")
  (can-redo? [this] "Return true if a state change can be redone.")
  (undo! [this] "Reverse one recorded state change. Return the restored state.")
  (redo! [this] "Re-apply a recorded state change. Return the re-applied state.")
  (clear-undo! [this] "Empty the redo! stack.")
  (clear-redo! [this] "Empty the redo! stack.")
  (pause-tracking! [this] "Pause recording state changes.")
  (resume-tracking! [this] "Resume recording state changes.")
  (stop-tracking! [this] "Stop tracking changes. Disconnect the watching function. Forget all saved state changes."))

(defrecord UndoManager [id tracked-atom undo-stack redo-stack paused watch-fn]

  UndoProtocol

  (init [this]
    (swap! undo-stack conj @tracked-atom)
    (add-watch tracked-atom id watch-fn)
    this)

  (num-undos [_] (count @undo-stack))

  (num-redos [_] (count @redo-stack))

  (can-undo? [this] (> (num-undos this) 1))

  (can-redo? [this] (pos? (num-redos this)))

  (undo! [this]
    (when (can-undo? this)
      (let [current-state (peek @undo-stack)
            _ (swap! undo-stack pop)
            restored-state (peek @undo-stack)]
        (swap! redo-stack conj current-state)
        (pause-tracking! this)
        (reset! tracked-atom restored-state)
        (resume-tracking! this)
        restored-state)))

  (redo! [this]
    (when (can-redo? this)
      (let [state-to-restore (peek @redo-stack)]
        (swap! redo-stack pop)
        (swap! undo-stack conj state-to-restore)
        (pause-tracking! this)
        (reset! tracked-atom state-to-restore)
        (resume-tracking! this)
        state-to-restore)))

  (clear-undo! [_] (reset! undo-stack []))

  (clear-redo! [_] (reset! redo-stack []))

  (pause-tracking! [_] (reset! paused true))

  (resume-tracking! [_] (reset! paused false))

  (stop-tracking! [this]
    (remove-watch tracked-atom id)
    (clear-undo! this)
    (clear-redo! this)))

(defn undo-manager
  "Return an initialized UndoManager object for the tracked atom."
  [tracked-atom]
  (let [id (keyword (str "undo-manager-tracker-" (goog/getUid @tracked-atom)))
        undo-stack (atom [])
        redo-stack (atom [])
        paused (atom false)
        watching-fn (fn [key atom old-state new-state]
                      ;(prn "-- Atom Changed --")
                      ;(prn "key: " key)
                      ;(prn "atom: " atom)
                      ;(prn "old-state: " old-state)
                      ;(prn "new-state: " new-state)
                      ;(prn "paused: " @paused)
                      ;(let [ae (.-activeElement js/document)]
                      ;  (when (= "TEXTAREA" (.-tagName ae))
                      ;    (let [sel-start (.-selectionStart ae)
                      ;          sel-end (.-selectionEnd ae)]
                      ;      (println "sel-start: " sel-start)
                      ;      (println "sel-end: " sel-end))))
                      (when-not @paused
                        (reset! redo-stack [])
                        (reset! paused true)
                        (swap! undo-stack conj new-state)
                        (reset! paused false)))]
    (init (UndoManager. id tracked-atom undo-stack redo-stack paused watching-fn))))
```

I believe this is my very first use of Clojure's [`defprotocol`](https://clojure.org/reference/protocols) and [`defrecord`](https://clojure.org/reference/datatypes#_deftype_and_defrecord) facilities.

You might use it like:

```clojure
(let [tracked-atom (atom "Initial State")
      ur (undo-manager tracked-atom)]
  ; Make your tracked changes here. Do your
  ; undo and redo operations by calling
  (ur/undo)
  ; or
  (ur/redo))
```

#### What This Isn't Good For

I learned these things because they are actually what I built it for and it was totally inadequate.

##### Text Editing

If your text is in an `atom`, editing the text while using this type of undo manager will make a copy of the complete contents of the atom on every keystroke.

Surprisingly, I never had problems with speed, probably because of Clojure's structure sharing. Likewise, memory consumption was never a problem either even with relatively large texts -- tens to hundreds of thousands of characters.

Also, using this simple approach doesn't really store enough information like where was the cursor? Was any text highlighted? And so on.

Of course, there are optimizations that could be made, like chunking state saves based on pauses in user activity. But if you're going to that much trouble, why not just user the [Command Pattern](https://en.wikipedia.org/wiki/Command_pattern)?

The bottom line is that it is just the wrong approach. Anyway, most text entry controls already do a fine job on their own.

##### When User-Visible Changes Consist of Two or More State Changes

Since this method captures _every_ change to the state of the atom, it can expose the user to previously invisible intermediate states causing confusion and panic. (Believe me I know!). 

For example, I've been working on a simple outliner. One of the commands lets you move the current headline up or down in the outline. Intermediate states in those operations may show duplicate headings or missing headings. Exposing the user to those intermediate states is not a good idea.

#### Summary

While using the Memento Pattern for undo/redo in Clojure is easy. But it is only applicable in simple situations that are invisible to the user.