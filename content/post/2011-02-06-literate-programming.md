---
title: Literate Programming
tags:
  - literate programming
  - mathematica
id: 109
comment: false
categories:
  - programming
date: 2011-02-06 01:38:36
modified: 2018-03-27T09:45:01.311313-04:00
---

Ever since [Donald Knuth](http://www-cs-faculty.stanford.edu/~uno/index.html) published "[Literate Programming](http://www.literateprogramming.com/knuthweb.pdf)" back in 1984, I've been a fan. Telling a computer how to run a program and helping a human understand it are two different but very necessary things.

<!--more-->

Of course all programming languages have a commenting facility. But often that is just not sufficient to explain what is going on in a program. In my own work, the typical commenting facilities simply do not have the power to describe algorithmic details efficiently, particularly in the area of mathematics. Knuth addressed the problem with his WEB, and later, [CWEB](https://en.wikipedia.org/wiki/CWEB) programs. He invented the [TeX](https://en.wikipedia.org/wiki/TeX) typesetting system and [LaTex](https://en.wikipedia.org/wiki/LaTeX) document preparation system in pursuit of this goal. His efforts have been embraced by the scientific community, particularly in the area of mathematics.

Others have been inspired to take up the cause as well. For example, the [FWEB](http://w3.pppl.gov/~krommes/fweb_toc.html) system provides literate programming tools for FORTRAN. The [Racket project](http://racket-lang.org/) has created a very powerful new tool to create documents for their Scheme-like group of languages. The [Scribble](http://docs.racket-lang.org/scribble/index.html) system provides a very comfortable tool for creating documentation and doing literate programming in the Racket environment. The article "[Scribble: Closing the Book on Ad Hoc Documentation Tools](http://www.cs.utah.edu/plt/publications/icfp09-fbf.pdf)" describes this system briefly. As you might expect, [detailed documentation](https://docs.racket-lang.org/scribble/index.html) exists elsewhere and is updated regularly.

Another recent tool is [Marginalia](http://blog.fogus.me/2011/01/05/the-marginalia-manifesto/) for [Clojure](http://clojure.org/) created by [Michael Fogus](http://blog.fogus.me/). Graphically, this tool takes a different approach. As the name suggests, it presents the program and commentary as two columns, sort of like you might annotate a book in the margins. I haven't used it seriously yet and it may not support the description of complex math (or maybe it does), but it is certainly a pleasure to read the output.

There doesn't seem to be a canonical system or tool for literate programming in Lisp, although there are a few [examples](http://gratefulfrog.net/) with [noweb](http://www.cs.tufts.edu/~nr/noweb/). <del>[This site](http://www.umcs.maine.edu/research/features/lit-prog-lisp.html)</del> (**Dead  Link**) seems to promise a new "system real soon now". The <del>[report](http://context.umcs.maine.edu/MaineSAIL/pubs/Papers/2010/lplisp.pdf)</del> (**Broken Link**) on the system looks promising.

However, of all the systems out there, the real champion for literate programming is [_Mathematica_](http://www.wolfram.com/mathematica/)®. _Mathematica_ is a computer mathematics systems at its heart. You can do extensive and sophisticated symbolic mathematics as well as create numerical results. Programming is done using a language that has some very Lisp-like characteristics. But the thing that makes it so useful for literate programming is the notebook interface. You can do everything you want in the notebook: write text with mathematics, do symbolic math, do numerical math, program, plot results and so on. The notebooks are "live", so you can make changes and recalculate at any time. You can send the notebooks to others with a _Mathematica_ system so they can change arguments, fiddle with settings, and experiment as they see fit. The notebooks can be converted to high quality PDF files for those who don't have _Mathematica_, although they won't be able to do the same manipulations.

My favorite use of _Mathematica_ is working through new algorithms that appear in the literature. I can create what I call "walkthroughs" of the paper describing the math, noting my interpretation, creating functions that implement the algorithms, plotting results, and so on. When completed, these can be shared with colleagues to help their understanding or to check my assumptions and interpretations. For example, I once worked through an unsupervised color deconvolution method for a project I was working on. It turned out that the algorithm did not work robustly for what we needed. However, a colleague at another site of my company was trying to get the same algorithm to work for a different purpose, but having difficulty with the implementation. A third colleague knew of my previous work and sent them a copy of the notebook. It produced the desired "Aha!" moment and someone got some use of my work.

_Mathematica_ has been around for a long time and is constantly improving. It's a great thing.