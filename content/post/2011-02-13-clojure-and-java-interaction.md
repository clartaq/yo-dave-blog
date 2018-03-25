---
title: Clojure and Java Interaction
tags:
  - Clojure
  - Java
id: 38
comment: false
categories:
  - Programming
date: 2011-02-13 02:10:46
---

One of my most-upvoted [answers](http://stackoverflow.com/questions/2181774/calling-clojure-from-java/2187427#2187427) on [Stackoverflow](http://stackoverflow.com/) is a simple example of how to call [Clojure](http://clojure.org/) functions from Java. It doesn't require calling through the Clojure run-time as so many responses do. But there is more to writing programs than calling static functions, as in my answer. You also might need to call methods _of_ objects and _on_ objects across the Clojure/Java divide.

<!--more-->

Here is the original answer I provided.

> <div>
> 
> It isn't quite as simple as compiling to a jar  and calling the internal methods. There do seem to be a few tricks to  make it all work though. Here's an example of a simple Clojure file that  can be compiled to a jar:
> 
>     
>     (ns com.domain.tiny
>       (:gen-class
>         :name com.domain.tiny
>         :methods [#^{:static true} [binomial [int int] double]]))
> 
>     (defn binomial
>       "Calculate the binomial coefficient."
>       [n k]
>       (let [a (inc n)]
>         (loop [b 1
>                c 1]
>           (if (&gt; b k)
>             c
>             (recur (inc b) (* (/ (- a b) b) c))))))
>     
>     (defn -binomial
>       "A Java-callable wrapper around the 'binomial' function."
>       [n k]
>       (binomial n k))
>     
>     (defn -main []
>       (println (str &quot;(binomial 5 3): &quot; (binomial 5 3)))
>       (println (str &quot;(binomial 10042 111): &quot; (binomial 10042 111))))
>     
> 
> If you run it, you should see something like:
> 
>     (binomial 5 3): 10
>     (binomial 10042 111): 49068389575068144946633777...
>     
> 
> And here's a Java program that calls the `-binomial` function in the `tiny.jar`.
> 
>     
>     import com.domain.tiny;
>     
>     public class Main {
>     
>         public static void main(String[] args) {
>             System.out.println(&quot;(binomial 5 3): &quot; + tiny.binomial(5, 3));
>             System.out.println(&quot;(binomial 10042, 111): &quot; + tiny.binomial(10042, 111));
>         }
>     }
>     
> 
> It's output is:
> 
>     (binomial 5 3): 10.0
>     (binomial 10042, 111): 4.9068389575068143E263
>  
> The first piece of magic is using the `:methods` keyword in the `gen-class` statement. That seems to be required to let you access the Clojure function something like static methods in Java.
> 
> The second thing is to create a wrapper function that can be called by Java. Notice that the second version of `-binomial` has a dash in front of it.
> 
> And of course the Clojure jar itself must be on the class path. This example used the Clojure-1.1.0 jar.
> 
> </div>
That's all well and good as far as it goes.

But the example was only intended to call static functions in Clojure code, such as I often do when writing statistics or mathematical programs. When you want to call classes in Clojure or into particular namespaces, it gets a little more complicated (but not much).

[This answer](http://stackoverflow.com/questions/4783344/calling-a-very-simple-clojure-function-from-java-does-not-work) shows how to create a class object in Clojure and call a method in that class. It is similar to calling a static function but you have to include a "this" pointer in the argument list to the clojure function. The "this" pointer is usually passed invisibly in other object-oriented languages like Java or C++.