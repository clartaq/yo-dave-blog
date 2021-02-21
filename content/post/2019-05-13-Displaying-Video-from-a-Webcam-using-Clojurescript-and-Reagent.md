---
author: david
title: Displaying Video from a Webcam using Clojurescript and Reagent
date: 2019-05-13T08:22:29.434-04:00
modified: 2019-05-13T16:24:11.885-04:00
tags:
  - Clojurescript
  - Reagent
  - video
  - webcam
---

As part of a book cataloging project, I want to be able to use a webcam on a desktop or laptop to scan [ISBN](http://isbn.org/about_ISBN_standard) (International Standard Book Number) numbers from the jackets of physical books. This isn't quite as easy as scanning barcodes with a phone or tablet because of focus, magnification, and depth of field characteristics of desktop and laptop cameras. (Or so I'm told. I really don't know much about it yet.)

But before you can scan barcodes, you need to be able to get video from the webcam in the first place. And because of my fondness for all things Clojure, I want to do this with Clojurescript and [Reagent](https://reagent-project.github.io).

It turns out that it isn't hard at all. But the relevant APIs for using the camera keep changing every few years. The most difficult part was filtering through all of the out-of-date information to come up with a working demonstration. There is a very nice article explaining how to make it work [here](https://www.kirupa.com/html5/accessing_your_webcam_in_html5.htm), but it is in JavaScript and doesn't use React.

Here is what I came up with:

```clojure
(ns cljs-webcam-demo.core
  (:require [reagent.core :as r :refer [atom]]))

(enable-console-print!)

(def app-state (r/atom {:video-element-id    "live-video"
                        :video-stream-object nil}))

(defn handle-video-success
  [stream]
  (let [video-element (.getElementById js/document
                                       (:video-element-id @app-state))]
    (swap! app-state assoc :video-stream-object stream)
    (set! (.-srcObject video-element) stream)))

(defn handle-video-error
  [error]
  (println "Error getting video: " (.-message error)))

(defn start-streaming
  [_]
  (-> js/navigator
      .-mediaDevices
      (.getUserMedia #js {:video true :audio false})
      (.then handle-video-success)
      (.catch handle-video-error)))

(defn stop-streaming
  [_]
  (when-let [v-stream (:video-stream-object @app-state)]
    (doseq [t (.getTracks v-stream)]
      (.stop t))
    (swap! app-state assoc :video-stream-object nil)))

(defn video-component
  [app-state-ratom]
  (fn [app-state-ratom]
    [:div.video-input {:style {:border "1px solid blue"}}
     [:video {:id       (:video-element-id @app-state-ratom)
              :autoPlay "true"}]]))

(defn home-page [app-state-ratom]
  [:div
   [video-component app-state-ratom]
   [:div
    [:button {:onClick start-streaming} "Start"]
    [:button {:onClick stop-streaming} "Stop"]]])

(r/render-component [home-page app-state]
                    (. js/document (getElementById "app")))
```

This is so simple that I haven't even created a repository for it.

I created the project using the [`figwheel` project template](https://github.com/bhauman/figwheel-template) for [leiningen](https://github.com/technomancy/leiningen).

After putting the text above into the `cljs-webcam-demo.core.cljs` file, you can run it by starting a leiningen REPL (`lein repl`) and executing the function `(fig-start)`.

The project should compile, and your default web browser should open to the page created by the program.

## Browser Compatibility ##

With **Safari**, it just worked. When the start button was clicked, Safari asked permission to use the camera. When given permission, it started displaying video. You can click the `start` and `stop` buttons to begin and end camera capture and display.

With **Firefox**, **Opera**, and **Brave**, things were a little more complicated. In each case,​ I had to do some browser-specific fiddling to get it to display the video. All of the fiddling was related to granting required privacy permissions. I don't remember the details -- I'm just glad it's done.

## Other Caveats ##

If you run this on a server, you will have to be using HTTPS. It just won't work with HTTP. Since I'm running under `figwheel` server from `localhost`, it works Ok. If I switch to running the program on a server from one of my local Raspberry Pis, I'll have to take care of setting up all the TLS security stuff. I'm just too lazy to do it now. I want to get on to reading barcodes!