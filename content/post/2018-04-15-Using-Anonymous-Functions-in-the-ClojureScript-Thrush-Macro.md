---
author: david
title: Using Anonymous Functions in the Clojure/Script Thrush Macro
date: 2018-04-15T11:48:25.120-04:00
modified: 2018-04-15T12:03:23.197-04:00
tags:
  - Clojure
  - ClojureScript
  - Macro
  - Thrush
---

The other day, I was putting together a sequence of operations to transform one piece of text into another form of that same text. The functions took a text argument, and the result was a slightly tweaked version. Put all those functions together to get the fully transformed result.

What could be more natural than to string those pieces of code together with one of Clojure's threading macros: '->' or '->>.' What I initially put together was something like:


```clojure
    (-> some-text
        (some-operation)
        (some-other-operation)
        #(some-simple-inline-function)
        (another-function-call)
        ...
```

But ClojureScript choked on the anonymous function. "Weird," I thought since I had just used it in the REPL to test it and it had worked fine.

It's all entirely explained [here](https://stackoverflow.com/questions/10757743/are-clojure-macros-always-leaky/10757945#10757945).

Long story short, when used in the threading macro like this, you need to set it up like this instead.

```clojure
    (-> some-text
        (some-operation)
        (some-other-operation)
        (#(some-simple-inline-function))
        (another-function-call)
        ...
```

Note the extra set of parentheses.