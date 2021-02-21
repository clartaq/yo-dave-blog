---
author: david
title: Letting the User Change the Sidebar Width in CWiki
date: 2019-06-27T16:51:20.635-04:00
modified: 2019-06-29T16:46:30.540-04:00
tags:
  - Clojure
  - ClojureScript
  - CSS
  - HTML
  - sidebar
  - technical note
---

Recently, I've been working on what I thought was a simple feature -- changing the width of the sidebar in the [CWiki](https://helixteamhub.cloud/Regolith/projects/binom-stats/repositories/cwiki/tree/default) wiki program.

There were two ways the user could do it.

First, they could set it manually in the "Preferences" page.

Second, and more naturally in my opinion, they could use the mouse to drag the separator between the sidebar and the main article area to the location they wanted it.

Implementing the first method was trivial, if tedious. Just extending the existing machinery for the Preferences page was all it took.

The second method, using the mouse, was surprisingly difficult.

The first thing I wanted to do was give the user an indication that there was some action they could take. There was already a vertical rule between the sidebar and article area. However, it was too thin to easily click with the mouse and there was no indication that something could be done even if you managed to click it.

A little CSS magic was all that was needed. Another `div` was added next to the existing border. The new `div` was normally transparent, but when the mouse passes over, it becomes visible, appearing as a thicker version of the vertical rule. It also shows a different cursor shape, a standard HTML 5 `col-resize` cursor.

The combination of the change in cursor shape and the apparent thickening of the border indicates that there is something different that you can do there. Clicking and dragging the mouse changes the width of the sidebar dynamically. On some browsers, the color of the divider changes as well.

The code that handles the (slightly) revised page layout is simple.

```Clojure
(defn sidebar-and-article
  "Return a sidebar and article div with the given content."
  [sidebar article]
  [:div {:class "sidebar-and-article"}
   sidebar
   [:div {:class "vertical-page-divider"}]
   [:div {:class       "vertical-page-splitter"
          :id          "splitter"
          ; Don't forget to translate the hyphen to an underscore. The false
          ; return is required for correct behavior on Safari.
          :onmousedown "cwiki_mde.dragger.onclick_handler(); return false;"}]
   [:article {:class "page-content"}
    article]])
```

The CSS that handles the hover and active states is pretty clever. I seem to have lost the link to the StackOverflow question where I found this.

```CSS
/* Set up some indicators for dragging the boundary. */

.vertical-page-divider {
    border-left: 1px solid var(--rule-color);
}

.vertical-page-splitter {
    display: flex;
    flex-direction: column;

    width: 10px;
    max-width: 10px;
    background-color: var(--rule-color);
    opacity: 0;
    transition: 0.3s;
}

.vertical-page-splitter:hover {
    cursor: col-resize;
    opacity: 1;
}

.vertical-page-splitter:active {
    cursor: col-resize;
    background-color: pink;
}
```

The code to change the width was the hard part. The first part of that puzzle was the event handler shown in the layout code above. For the longest time, I had the click handler as:

```clojure
"cwiki_mde.dragger.onclick_handler();"
```

And that mostly worked. Note the use of the underscore character "_" rather than the more idiomatic hyphen. It has to be an underscore.

However, it didn't work perfectly with Safari. In Safari, the cursor changed shape correctly, but once the mouse moved, the cursor changed back to an "I-beam" text cursor. That was fixed by changing the call to:

```clojure
"cwiki_mde.dragger.onclick_handler(); return false;"
```

Note the addition of `return false;` at the end. You need that to make Safari work.

The code referred to in the event handler is in a new namespace: `cwiki-mde.dragger`.

```clojure
;;;;
;;;; A utility function to handle dragging the border between the sidebar
;;;; and article portion of the page. To allow changing the width of the
;;;; sidebar with a mouse drag.

(ns cwiki-mde.dragger
  (:require [ajax.core :refer [ajax-request text-request-format
                               text-response-format]]))

;; The minimum allowable width for the sidebar.
(def ^{:private true :const true} min-sidebar-basis 150)

;; Could calculate this, but this is easier. This value must match that
;; used in the css file for the sidebar.

(def ^{:private true :const true} twice-padding-width 48)

;; The resolved elements, just so we don't have to keep recalculating them.

(def ^{:private true} sidebar-ele (atom nil))

;; Other state.

(def ^{:private true} dragging (atom false))
(def ^{:private true} starting-mouse-x (atom 0))
(def ^{:private true} starting-basis (atom 0))
(def ^{:private true} new-basis (atom "0px"))

(defn response-handler [[ok response]]
  (when-not ok
    (.error js/console (str "Something bad happened: status: "
                            (:status response)
                            ", status-text: " (:status-text response)))))

(defn persist-new-basis
  [new-basis]
  (ajax-request
    {:uri             "/width-of-sidebar"
     :method          :post
     :body            new-basis
     :handler         response-handler
     :format          (text-request-format)
     :response-format (text-response-format)}))

(defn- move [evt]
  (when @dragging
    (let [movement (- (.-pageX evt) @starting-mouse-x)]
      (reset! new-basis (str (max min-sidebar-basis
                                  (+ @starting-basis movement)) "px"))
      (set! (-> (.-style @sidebar-ele) .-flexBasis) @new-basis))))

(defn- stop-tracking [_]
  (when @dragging
    (reset! dragging false)
    (.removeEventListener js/window "mousemove" move)
    (when (not= @starting-basis @new-basis)
      (persist-new-basis @new-basis))))

(defn- start-tracking [evt]
  (reset! starting-mouse-x (.-pageX evt)))

(defn ^{:export true} onclick_handler []
  (reset! sidebar-ele (.getElementById js/document "left-aside"))
  (reset! starting-basis (- (.-offsetWidth @sidebar-ele) twice-padding-width))
  (reset! dragging true)
  (.addEventListener js/window "mousedown" start-tracking)
  (.addEventListener js/window "mousemove" move)
  (.addEventListener js/window "mouseup" stop-tracking))
```

I couldn't figure out a way to pass arguments or get data back from a handler that was called from Clojure/Hiccup. In fact, I was surprised that calling ClojureScript from Clojure worked at all. That's why there is an apparently pointless `start-tracking` function -- I could have done the same thing in the `onclick_handler`, but I needed the event.

For the longest time, I couldn't figure out what might be a good way to pass the new width from the ClojureScript client code back to the server running Clojure. There is [WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) machinery elsewhere in the client to handle communication back and forth between the editor and the server. But that seemed like it could be unreliable and buggy. Eventually, I looked into [AJAX](https://en.wikipedia.org/wiki/Ajax_%28programming%29), something I had never tried before. The [`cljs-ajax`](https://github.com/JulianBirch/cljs-ajax) library made it pretty easy though.

So, this code takes care of moving the divider and updating the layout as the mouse is moved. When the user completes the drag operation, if the new position of the divider differs from the original, an AJAX call is made back into the server code in Clojure to persist the data.

Here's the function in the `cwiki.routes.home` namespace that saves the new width to the options table in the database.

```clojure
(defn post-sidebar-basis
  "Persist the sidebar width basis."
  [req]
  (let [body ^BytesInputStream (:body req)
        new-basis (safe-parse-int (slurp (.bytes body)))]
    (db/set-option-value :sidebar_width new-basis)
    {:status  200}))
```

All of the page layout functions that put together a view with a sidebar were changed to use the value from the database rather than rely on the value in the CSS file.

Just for completeness, here's the `safe-parse-int` function used above.

```clojure
(defn- safe-parse-int
  "Convert a string to an integer. Return -1 on exception. Parses the first
  (and only the first) contiguous group of digits."
  [x]
  (try (Integer/parseInt (re-find  #"\d+" x))
       (catch Exception _
         -1)))
```

It's just a handy little utility function that I use here and there.

Now the user can change the width of the sidebar to whatever is useful/attractive to them.
 