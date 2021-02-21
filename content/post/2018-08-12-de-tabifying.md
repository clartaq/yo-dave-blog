---
author: david
title: De-Tabifying
date: 2018-08-12T13:36:36.206-04:00
modified: 2018-08-13T14:59:37.532-04:00
tags:
  - memoization
  - programming
  - text processing
---

There is probably no more boring task in programming than processing a file to replace tabs with spaces or doing the reverse and replacing spaces with tabs. And yet it has started religious wars that have raged for years in various programming communities.

This "tabifying" or "de-tabifying" is not something I have to do frequently, but every once in a while, I need to convert tabs to spaces in some text. Sometimes I need to do it repeatedly and quickly. For example, in a Markdown editor, I basically need to do a de-tabify on every keystroke. It can't take long regardless of how long the input text is. Here's what I came up with.

```clojure
(defn- blanks
  "Return a string consisting of the number of spaces requested."
  [how-many]
  (str/join (repeat how-many \space)))

(defn- make-blank-array
  "Return a vector of strings containing only blank characters. The vector
  consists of strings of blanks of size 'most' down to one."
  [most]
  (loop [cnt most out []]
    (if (zero? cnt)
      out
      (recur (dec cnt) (conj out (blanks cnt))))))

; Memoize so that we don't regenerate on every keystroke​.
(def ^:private memoized-blank-array (memoize make-blank-array))

(defn detab
  "Replace any tabs in the input text with spaces. If no tab spacing is
  set in the options, a default value of 4 is used."
  ([text]
   (detab text {:tab-spacing 4}))
  ([text options]
   (let [tab-spacing (or (:tab-spacing options) 4)]
     (if-not (or (nil? text)
                 (empty? text)
                 (str/includes? text \tab))
       text
       ; otherwise
       (let [spcs (memoized-blank-array tab-spacing)]
         (loop [in text out "" offset 0]
           (if (empty? in)
             out
             (let [nc (first in)
                   rnc (if (= nc \tab)
                         (get spcs offset)
                         nc)
                   ofs (if (= nc \newline)
                         0
                         (mod (+ offset (count rnc)) tab-spacing))]
               (recur (rest in) (str out rnc) ofs)))))))))

```

It's pretty straightforward. Assuming the text needs to be de-tabified at all, just run through the text character-by-character keeping track of where the current character is relative to the beginning of the line. If a tab occurs​, determine the number of spaces to insert based on the offset from the beginning of the line, _i.e._ where the next tab stop occurs. Get a pre-computed string of spaces by doing a little table lookup and replace the tab. Then just keep going.

The only tricky thing in the above implementation is "memoizing" the table of pre-computed replacement strings.