---
title: sorted-set-by
date: 2018-01-19 13:49:13 
tags:
  - clojure
categories:
  - programming
---

This is just a little note about one of my favorite datatypes in [Clojure](https://clojure.org/).

Like most Lisps, Clojure has a very useful group of datatypes built in including some `set` types. I use `sorted-set` a lot. Until recently, I hadn't noticed `sorted-set-by`, a function that returns a sorted set using a comparator you specify. I found myself needing to create a sorted set of strings where the sorting was case-insensitive. The `sorted-set-by` function was exactly what I needed.

Here's an example of usage.

```
(defn- case-insensitive-comparator
  "Case-insensitive string comparator."
  [^String s1 ^String s2]
  (.compareToIgnoreCase s1 s2))

(defn get-all-users
  "Return a sorted set of all of the user names known to the wiki."
  ([]
   (get-all-users h2-db))
  ([db-name]
   (when-let [user-array (jdbc/query db-name ["select user_name from users"])]
     (into (sorted-set-by case-insensitive-comparator)
           (mapv #(:user_name %) user-array)))))
```

The `case-insensitive-comparator` function is probably a bit of over-engineering. Since this comparator just calls the built-in Java comparator, I could have used the `.compareToIgnorecase` function more directly.