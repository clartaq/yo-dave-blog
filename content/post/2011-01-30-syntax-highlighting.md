---
title: Syntax Highlighting
tags:
  - highlighting
id: 86
comment: false
categories:
  - blogging
date: 2011-01-30 01:11:28
modified: 2018-03-15T16:41:00.360344-04:00
---

**Update 2017-08-19**: This post refers to a time when the blog was hosted in WordPress. It isn't really applicable anymore. But it may provide some guidance to someone who happens to come across it.

Since this blog is mostly about programming, there are a lot of code listings. To make those listings more easily readable, highlighting syntax in the listings is a good thing.

Of all of the syntax highlighters I have tried, the [SyntaxHighlighter](http://alexgorbatchev.com/SyntaxHighlighter/) JavaScript tool by Alex Gorbachev is the one I most prefer. The [SyntaxHighlighter Evolved](https://wordpress.org/plugins/syntaxhighlighter/) is a nice WordPress plugin wrapped around the Gorbachev package. Setting things up in WordPress is trivial and just works.

Although the plugin comes with many "brushes" for different languages, the one for Clojure is a bit simplistic. However, Brian Carper has taken the time to create <del>[a more sophisticated highlighter for Clojure](http://briancarper.net/blog/556/)</del> (**Broken Link**). He has even supplied a tweaked theme. The theme doesn't suit my tastes, but it looks good on his site.

Brian's Clojure brush even seems to work well with Lisp

```clojure
(defun verbose-sum (x y)
  &quot;Sum any two numbers after printing a message.&quot;
  (format t "Summing ~d and ~d.~%" x y)
  (+ x y))
```

and Scheme

```clojure
(define product
  (lambda (ls)
    (call/cc
     (lambda (break)
       (let f ([ls ls])
         (cond
           [(null? ls) 1]
           [(= (car ls) 0) (break 0)]
           [else (* (car ls) (f (cdr ls)))]))))))
```
 