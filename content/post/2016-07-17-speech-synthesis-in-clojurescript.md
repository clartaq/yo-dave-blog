---
title: Speech Synthesis in ClojureScript
tags:
  - programming
  - clojurescript
  - voice
date: 2016-07-17 12:35:08
modified: 2018-03-25T16:01:06.923778-04:00
---

Just like everyone else, it seems, I've been following all of the news about voice-activated personal assistants. There are all the commercial offerings like Siri, Alexa, Cortana, and so on, as well as some DIY projects on the web, like this [one](https://blog.truthlabs.com/rethinking-voice-search-2496640fdec2#.uvnrkmji0) and [this one](https://howchoo.com/g/yti5mmq0ntu/add-voice-controls-to-your-raspberry-pi-using-jasper) and [this one](http://www.instructables.com/id/Raspberri-Personal-Assistant/).

These types of projects typically involve a front end that converts voice to text, some middle piece that interprets the text and obtains some answer or creates an action, ending up with a voice response by the system back to the user. I have some (out-of-date) experience with speech to text, but not the other end of the process: text to speech. So here's a little investigation into how to do it with ClojureScript. Turns out that it is almost trivial these days.

<!--more-->

Since all great programmers are lazy thieves, I took this lesson to heart and basically ripped-off this pre-existing [example in JavaScript](http://blog.teamtreehouse.com/getting-started-speech-synthesis-api). I used the CSS for the project almost verbatim. To create a ClojureScript project, I used [Leiningen](http://leiningen.org/):

```shell
lein new figwheel voice-demo --reagent
```
Of course, `[figwheel](https://github.com/bhauman/lein-figwheel)` is there since it is such a help with ClojureScript development. The `--reagent` flag means that the project should be created to use the marvelous [Reagent](https://reagent-project.github.io/) library. Reagent is used primarily to do intelligent, dynamic updates to the DOM, but for this project, it is only used for its ease of creating UI components with the [Hiccup syntax](https://github.com/weavejester/hiccup/wiki/Syntax).

You can grab a copy of the code from the [repository on Bitbucket](https://bitbucket.org/David_Clark/voice-demo). If you look in the README file and execute the command:

```shell
lein figwheel
```

you will build the project and start it running in your default browser. If your browser supports speech synthesis, the browser page should look like this:

[![Speech Synthesis UI Clip](/static/img/2016-07-17-Speech_Synthesis_UI_Clip.PNG "Speech Synthesis UI")<br><small>Speech Synthesis UI</small>](/static/img/2016-07-17-Speech_Synthesis_UI_Clip.PNG)

In my testing on Windows 10, only [Firefox](https://www.mozilla.org/en-US/firefox/new/) and [Chrome](https://www.google.com/chrome/) supported the necessary API. Just type something into the empty text box and click the big button at the bottom labeled "Speak". You can also fiddle with the voice used, the volume it speaks at, the rate it speaks, and the pitch.

The code is pretty straightforward. It first determines if the browser supports speech synthesis and constructs the appropriate version of the UI and awaits your input. Most everything else is dealing with the settings you've made and speaking.

The only moderately tricky thing about the code is how to load the voices. For Firefox, you just load them. But Chrome loads voices asynchronously. That means they may load after the DOM has been built. Also, they may reload at other times, hence this code that builds the GUI when speech synthesis is supported. The call to` load-voices` upon detection of the `"DOMContentLoaded"` event loads the voices for Firefox but typically loads nothing when running Chrome. The listener for `"onvoiceschanged"` calls the function to load the voices on Chrome.

```clojure
(defn supported-page []
  (.addEventListener js/window "DOMContentLoaded" load-voices)
  (let [speech-syn (.-speechSynthesis js/window)]
    (set! (.-onvoiceschanged speech-syn) load-voices))
  [:div
   [:p {:id "is-supported"} "Your browser " [:strong "supports"] "speech synthesis."]
   [speech-msg]
   [voice-selection]
   [option-sliders]
   [:button {:id "speak" :on-click handle-click} "Speak"]])
```

Likewise, since` load-voices` may be called multiple times, it needs to clear out any previous contents in the drop-down selection box.

```clojure
(defn load-voices []
  (let [speech-syn (.-speechSynthesis js/window)
        voices (.getVoices speech-syn)
        va (js->clj voices)
        voice-selecter (.getElementById js/document "voice")]
    ;; clear any existing choices
    (while (pos? (.-length voice-selecter))
      (.remove voice-selecter "0"))
    (doall (for [v va]
             (let [option (.createElement js/document "option")
                   name (.-name v)]
               (aset option "value" name)
               (aset option "innerHTML" name)
               (.appendChild voice-selecter option))))))
```

Still, getting things to start working is pretty easy.

Although working with Chrome is more troublesome. It seems to support many more voices, at least on my system. It is kind of fun to hear how all of the ex-US voices pronounce things.