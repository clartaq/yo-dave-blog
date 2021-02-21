---
author: david
title: Detecting from a Web Client Whether a Particular Font is Installed
date: 2019-06-03T17:31:32.736-04:00
modified: 2019-06-04T13:44:24.035-04:00
tags:
  - client-side
  - CSS
  - detection
  - font
---

Sometimes, a web client just needs to know what font it is using. The use cases may vary, but when you need it, you need it.

In my own case, I have a tag editing component for my personal wiki. The component allows entry of any number of tags. It also changes the size of the input as the user types in the text, getting smaller when characters are deleted or larger when new characters are entered.

In order to make accurate measurements of the width of the text, the tag editor(s) need to know what font they are using.

That can be a surprisingly difficult question to answer.

One approach is to specify and use a web font. For various reasons that may not be possible. For example, the user may have turned off the ability of their browser to download web fonts because of a slow internet connection. Or perhaps their employer prohibits such downloads.

For my use, I typically specify a `font-family` in the CSS for components in my programs so that I have graceful fallback regardless of which OS and what version I am using. But the browser will not tell you explicitly which font it has selected to use from the suggestions you have provided. And without knowing exactly which font is in use, it is impossible to make accurate measurements of text to be displayed.

What to do?

## The Testing Concept ##

### An Algorithm ###

One idea that has been bouncing around for many years is to:

- Start by measuring the width of a sample string using a generic font. (The last time I [checked](https://developer.mozilla.org/en-US/docs/Web/CSS/font-family), there were nine generic fonts supported: `serif`, `sans-serif`, `monospace`, `cursive`, `fantasy`, `system-ui`, `emoji`, `math`, and `fangsong`.) 
- Then construct a `font-family` with the font of interest and the generic font.
- Render the same string with the `font-family`. 
- If the font of interest is not installed, the width measurement will be identical to that produced by rendering with the generic font.

### Some Finer Points ###

In point of fact, you must test against more than one generic font since you might want to ask if a font that is used as a generic is installed. For example, if you want to know if Arial is installed and run this test, you would receive a false negative on a system where Arial is used as the generic `sans-serif` font.

In the code I use, I test against three generic fonts, `serif`, `sans-serif`, and `monospace`.

Also, what size should the sample string be and what letters should it consist of? One of the earliest implementations of this method is attributed to one "Lalit Patel" (http://www.lalit.org/lab/javascript-css-font-detect (broken link)). They propose a string consisting of wide letters like "M" and "W" and thin letters like "l" and "i". They also suggest using a large font size. I have no reason to quibble with these choices. I use M. Patels [suggestions](https://www.bitbook.io/using-javascript-to-detect-installed-fonts/) of "mmmmmmmmmmlli" for the sample string and 72 pixels for the size.

### How to "Render" and "Measure" the Sample String ### 

You may have noticed that I have been playing fast and loose with some terminology here. What does it mean to "render" the string? You don't want to render to the screen; this should all happen invisibly in the background.

There have been different approaches but the one I prefer is to create an HTML `canvas` element (unconnected to the DOM), get its 2-dimensional context, set the font size and family, and use the `measureText` method to get the width.

### Which Font from a `font-family` Declaration is Actually Being Used? ###

The rule is that when a CSS `font-family` is applied to an element the actual font used is the first one that is found to be installed. For example, if a CSS rule is applied to `body` like so:

```css
body {
    font-family: Boojum, Woojum, Helvetica, Wanger, Banger, serif;
}
```

In all likelihood, "Helvetica" will be the selected font. Writing a function to figure out which font will be applied is trivial.

## Some Code ##

Here's my implementation in ClojureScript:

```clojure
;;;;
;;;; Utilities to help with determining what if particulare fonts are
;;;; installed on the client system.
;;;;

(ns cwiki-mde.font-detection
  (:require [clojure.string :as string]))

;; Filled in during loading of namespace.
(def measured-font-widths (atom {}))

(def generic-fonts [:monospace :serif :sans-serif])

;; Suggested by Lalit Patel
;; Website: http://www.lalit.org/lab/javascript-css-font-detect/ (broken)
(def test-string "mmmmmmmmmmlli")
(def test-size 72)

(def canvas (.createElement js/document "canvas"))
(def context (.getContext canvas "2d"))

(defn- measure-string-width
  "Return the width in pixels required to render the string in the given
  font at the given size."
  [s font-name font-size]
  (set! (.-font context) (str font-size "px " font-name))
  (.-width (.measureText context s)))

(defn- init-generic-font-widths!
  "Set the global variable containing the measured widths of the test
  string at the test size for each of the test fonts."
  []
  (mapv #(swap! measured-font-widths
                merge
                {% (measure-string-width test-string (name %) test-size)})
        generic-fonts))

(init-generic-font-widths!)

(defn- measure-against-generic
  "Measure the width of the test string using the font name and compare it
  to the width when one of the generic fonts (given by the generic-key) is
  used. Return true if the width does NOT match the width produced by the
  generic font. Return false otherwise."
  [font-name generic-key]
  (let [family (str test-size "px " font-name ", " (name generic-key))]
    (set! (.-font context) family)
    (let [width (measure-string-width test-string family test-size)]
      (not= width (generic-key @measured-font-widths)))))

(defn font-available?
  "Return true if the font named is available on the system."
  [font-name]
  (reduce #(or %1 (measure-against-generic font-name %2)) false generic-fonts))

;; Usage:
;;
;; (println "(font-available? \"Calibri\"): " (font-available? "Calibri"))
;; => true on Windows, false on Mac
; (println "(font-available? \"Calibri Regular\"): " (font-available? "Calibri Regular"))
;; => false
;; (println "(font-available? \"Arial\"): " (font-available? "Arial"))
;; => true
;; (println "(font-available? \"Boojum\"): " (font-available? "Boojum"))
;; => false
;; (println "(font-available? \"Helvetica Neue\"): " (font-available? "Helvetica Neue"))
;; => false on Windows, true on Mac
;; (println "(font-available? \"Helvetica Newish\"): " (font-available? "Helvetica Newish"))
;; => false

(defn font-family->font-used
  "Given a CSS font family, determine which is actually used. This is the
  first name in the list that is actually installed. Return nil if none of
  the fonts in the list are installed."
  [font-family]
  (let [names (mapv string/trim (string/split font-family #","))]
    (some #(when (font-available? %) %) names)))

;; (println "Selected from headline font family: " (font-family->font-used "\"Century Gothic\", Muli, \"Segoe UI\", Arial, sans-serif"))
;; => Muli ; on my system
;; (println "Selected from body font family: " (font-family->font-used "Palatino, \"Palatino Linotype\", \"Palatino LT STD\", \"Book Antiqua\", Georgia, serif"))
;; => Palatino ; on my system
;; (println "Selected from fixed font family: " (font-family->font-used "Consolas, \"Ubuntu Mono\", Menlo, Monaco, \"Lucida Console\",\n    \"Liberation Mono\", \"DejaVu Sans Mono\", \"Bitstream Vera Sans Mono\",\n    \"Courier New\", monospace, serif"))
;; => "Ubuntu Mono" ; on my system
```

The two important public functions are:

- `font-available?` used to determine if a particular font is installed.
- `font-family->font-used` that will return the name of the font that will be used in the browser from a `font-family` declaration.

So far, this code has proven small, quick, and robust. I hope you find it useful.