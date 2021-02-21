---
author: david

title: File Operations in Scheme

date: 2021-02-19T17:58:30.041-05:00
modified: 2021-02-21T13:39:58.517-05:00
tags:
  - Scheme

  - word list

  - files

  - Lisp

  - dictionary

---

A little word game that I've been writing requires a list of acceptable words. For the game, acceptable words should be defined similarly to popular word games the player already knows like Scrabble®
or Words with Friends. Luckily word lists for such games are readily available.

For my little game, the list from Words with Friends seemed like the best choice. But it has about 175,000 English words. Way to many to use routinely in development. And they are all lengths from two characters to more than 10. I wanted some way to create a good sampling of words from three to nine characters in length. And I wanted to use [Scheme](schemers.org) to do it.

So I wrote the little Scheme script described here to do it. I used [Chez Scheme](scheme.com) and obtained the wordlist [here](https://www.wordgamedictionary.com/word-lists/words-with-friends/).

The script starts off with a few definitions.

```scheme
(define skip-by 1000)
(define input-list-file-name  "./word-lists/ENABLE_word_list.txt")
(define output-list-file-name "./word-lists/subset_list.txt")
```

The `skip-by` means that I want to use every 1000th word. Starting from a list of 175,000 words should produce an output file of about 175 words. Not too big. Not too small.

The file names are just where I put the input and output files relative to the directory where the script is run.

The actual program starts off like this:

```scheme
(define (make-word-list)
  (let ((inp (open-input-file input-list-file-name))
        (outp (open-output-file output-list-file-name '(replace))))
    (dynamic-wind
      (lambda () #f)
      (lambda () (process-files inp outp))
      (lambda ()
        (close-port inp)
        (close-port outp)))))
```

The operation of this definition should be pretty obvious. Open the input and output files, do the processing, and close the files. The `dynamic-wind` procedure is used to protect the file ports to assure that they are closed even in the event of an error. This is a great and easy way to assure that the finalization code for resources is always executed.

Note that opening the output port includes an optional argument, `'(replace)`. That means any earlier version of the output file will be overwritten. The program will not stop because a file with the same name is already present. It's very convenient when doing experimental development work, but be careful.

The `make-word-list` procedure just passes the real work off to `process-files`.

```scheme
(define (process-files in-port out-port)
  (let loop ((line (get-line in-port))
             (inp-cntr 0)
             (out-cntr 0))
    (if (eof-object? line)
        (begin
          (println "Done!")
          (println "Words in input file: " inp-cntr)
          (println "Words in output file: " out-cntr))
        (loop (get-line in-port) (+ 1 inp-cntr)
              (process-line out-port line inp-cntr out-cntr)))))
```

All this procedure does is read lines (one word each) and pass the work on to `process-line` to figure out what to do. When there are no more lines, `process-files` just displays some information and quits.

The `println` procedure is a simple utility function included in most of my programs.

```scheme
(define (println . args)
  (for-each display args)
  (newline))
```

I find this preferable to writing a bunch of calls to `display`.

Finally, we get to `process-line` where a little actual work is done.

```scheme
(define (process-line out-port line inp-cntr out-cntr)
  (if (and (zero? (mod inp-cntr skip-by))
           (< (string-length line) 10)
           (> (string-length line) 2))
      (begin
        (display line out-port) (newline out-port)
        (+ 1 out-cntr))
      out-cntr))
```

The procedure checks if it has skipped the required number of words then checks if the word is within the desired number of characters. If so, the word is written to the output file, the one associated with `out-port`.

The script ends with `(make-word-list)` to run the script when the file is `load`ed.

The script works fine:

```shell
Chez Scheme Version 9.5.4
Copyright 1984-2020 Cisco Systems, Inc.

> (load "subset-dictionary.ss")
Done!
Words in input file: 172820
Words in output file: 105
>
```

So, of the roughly 172,000 words in the original file, and from the 172 words left after taking every 1000th word, 105 were the correct length.

That's enough to use during development.

There are still a few bothersome details though. 

1. The structure of the program is being determined by the desire to keep procedures mostly functional. For example, I might not have broken out `process-line` as a separate function, but I couldn't think of a way to increment `out-cntr` in `process-files` without using `set!`.

2. The argument list to `process-line` is a bit long.

3. The limits on word lengths should probably be parameterized somewhere rather being being hard-wired "magic numbers".

These are just aesthetic issues though.

There is a gist showing the complete file [here](https://gist.github.com/clartaq/d0651636deb6e5e24168aac393d8d0e4).