---
author: david
title: Notes on ClojureScript Regular Expressions
date: 2018-04-20T10:14:36.113-04:00
modified: 2018-04-20T14:04:24.777-04:00
tags:
  - Clojure
---

There's an old saying about regular expressions -- paraphrasing: "If you try to solve a problem with regular expressions, you then have two problems."

Even though regular expressions are present in most programming languages, I have never become particularly proficient with them. Those that I am most familiar with are the Java version. Since ClojureScript compiles to JavaScript and runs without the Java underpinnings, it's version of regular expressions are different.

They are different enough that I had to do a little experimentation to get them to work as needed.

## Syntax ##

Consider a snippet of JavaScript to normalize line endings in some text:

```javascript
   // Converts \r\n and \r to \n.
   fixEolChars = function (text) {
       text = text.replace(/\r\n/g, "\n");
       text = text.replace(/\r/g, "\n");
       return text;
   };
```
This function converts DOS, WIndows, and Mac line endings to the traditional Unix form. (This snippet is from the repository for the [PageDown](https://github.com/StackExchange/pagedown) Markdown editor.)

Something in ClojureScript is similar but has some subtle differences.

```clojure
    (defn fix-eol-chars
      "Converts Windows/DOS line endings (\r\n) and Mac line endings (\r) to
      the Unix/Linux standard single line feed (\n)."
      [text]
      (-> text
         (str/replace #"\r\n" "\n")
         (str/replace #"\r" "\n")))

```
Notice that the syntax for a regular expression literal is the same as for Clojure, using the `#"xxx"` reader macro. The contents are not the same though. JavaScript uses a slash character, `/`, to indicate the beginning and end of the regular expression; ClojureScript does not. 

## Modifiers ##

JavaScript also allows appending modifiers after the regular expression itself. See that "g" character at the end? As most of you probably know already, that means making the search and replace "global." JavaScript also supports the "i" modifier (to ignore character case when searching) and the "m" modifier (for multi-line searches).

But you don't see any modifiers in the ClojureScript version. That's because the library code always appends the "g" flag when it constructs the JavaScript regex from its arguments. (The source to the ClojureScript version of `clojure.string.cljs` is [here](https://github.com/clojure/clojurescript/blob/master/src/main/cljs/clojure/string.cljs)).

Great! But how do you add the "i" and "m" modifiers when needed? It turns out you can do it simply in the regular expression literal itself by pre-pending a sequence of the form "(?xxx)" where the "xxx" part consists of the modifiers you want to apply. For example, here's how you might replace all lines composed of only tabs or spaces with an empty string.

```clojure
    (str/replace text #"(?m)^[ \t]+$" "")
```

## Matches ##

Both the JavaScript and ClojureScript versions of the replace function let you specify the replacement text as a string or as the return value of a function. When you use a string, both JavaScript and ClojureScript let you put part of the matched string into the replacement using some special characters. The doc-string for `string.replace` includes the following example to let you translate from English to Pig Latin.

```clojure
    (clojure.string/replace \"Almost Pig Latin\" #\"\\b(\\w)(\\w+)\\b\" \"$2$1ay\")
    -> \"lmostAay igPay atinLay\""
```

When you provide a function to produce the replacement text, it gets arguments related to the matched text. In ClojureScript, there is a single argument, a variable length vector. The first element is the entire match. If any parts of the match were captured by parenthesized groups in the regular expression, they appear as following elements in the vector.

For example, here is a snippet (again, derived from the PageDown editor source) that detects Markdown headers and translates them to HTML.

```clojure
        (str/replace #"(?m)^(\#{1,6})[ \t]*(.+?)[ \t]*\#*\n+"
                     (fn [arg-vec]
                       (let [h-level (count (second arg-vec))]
                         (str "<h" h-level ">" (third arg-vec)
                              "</h" h-level ">\n\n"))))
```

In this code, the second element of `arg-vec`, _i.e._ the part of the match captured in the first parenthesized group, contains the leading pound signs "#" that precede a heading in Markdown. The number of pound signs is used to determine the heading level. Further down, the third element contains the content of the header and is pasted between the HTML tags for the header. (`third` is a function I defined elsewhere as: `(defn third [arg-vec] (nth arg-vec 2))`)

## Conclusion ##

Finding information about regular expressions in ClojureScript (as opposed to Clojure) was a bit difficult, and some of this was gleaned from trial and error.

1. ClojureScript regular expressions can be entered as literals similar to the way it is done in JavaScript.
    1. Use the ClojureScript regular expression literal syntax: #"".
    2. Don't include the `\` characters used to mark the beginning and end of a JavaScript regular expression.
2. You can specify the modifiers to use in ClojureScript by pre-pending them to the regular expression literal.
3. The ClojureScript `string/replace` function always does a global search. You don't need to use the "g" modifier.
4. You can use parts of the matched regular expression in the replacement text, whether using a literal string or the result of a function.




    