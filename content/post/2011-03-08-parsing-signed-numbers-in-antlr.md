---
title: Parsing Signed Numbers in ANTLR
tags:
  - antlr
  - parsing
id: 219
comment: false
categories:
  - programming
date: 2011-03-08 15:58:17
modified: 2018-03-15T17:46:32.403139-04:00
---

One of the more interesting things I've been working on lately is a scripting language to drive a simulation system. It describes nominal values of key parameters, how to vary them, do an analysis, gather the results and present those results. [ANTLR](http://www.antlr.org/ "ANTLR Home Page") and [ANTLRWorks](http://www.antlr3.org/works/ "ANTLRWorks Home Page") are usually great tools to create the lexer and parser of such a scripting language. That's how I started out this time too. Since I thought it would be trivial, I left parsing signed numbers to nearly the end of the project. It was a bit of a surprise how difficult this was.

<!--more-->For this work, I needed both signed and unsigned integers and floating point numbers. Specifying things like the number of replicates requires a non-negative integer. Specifying things like wavelengths requires non-negative floats. Specifying how to step parameters from one simulation run to the next requires signed integers or floats.

After the first few failed attempts, it was off to the web, where there was no shortage of advice and pointers to code. None of it worked. Oh, there were lots of methods that parsed unsigned integers, a few working examples of unsigned floats, and one very large example of the grammar to parse numbers in JavaFX. But none of them worked for my application. I'm not giving pointers here since that code didn't work as expected.

What finally _did_ work was a trivial modification to the relevant piece of a [grammar for C](https://github.com/antlr/grammars-v4/blob/master/c/C.g4 "An ANTLR Grammar for ANSI C") that was written by ~~[Terence Parr](http://www.cs.usfca.edu/~parrt/ "Terence Parr") (the creator of ANTLR) back in 2006~~  Sam Harwell back in 2013.

So, here's the modification that worked for me.

```bash
number
 :    unary_operator? unsigned_number
 ;

unary_operator
 :    '+'
 |    '-'
 ;

unsigned_number
 :    UNSIGNED_INT
 |    UNSIGNED_FLOAT
 ;

UNSIGNED_INT : ('0' | '1'..'9' '0'..'9'*);

UNSIGNED_FLOAT
 :   ('0'..'9')+ '.' ('0'..'9')* Exponent?
 |   '.' ('0'..'9')+ Exponent?
 |   ('0'..'9')+ Exponent
 ;

fragment
Exponent : ('e'|'E') ('+'|'-')? ('0'..'9')+ ;
```

When you want an unsigned integer, use `UNSIGNED_INT`. When you want a signed integer or float, use `number`, and so on.

Just to point out one detail, this grammar produces a lexer that recognizes numbers that your underlying implementation language may not be able to represent. For example, the lexer recognizes 1.347e-47026 as a legitimate number even though you can't represent it in Java. That's something for other parts of the program to handle.