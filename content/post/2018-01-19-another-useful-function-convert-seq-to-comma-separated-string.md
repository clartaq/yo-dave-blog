---
title: Another Useful Function -- convert-seq-to-comma-separated-string
date: 2018-01-19 14:32:49 
modified: 2018-03-17T13:20:23.819808-04:00
tags:
  - clojure
  - utility
categories:
  - programming
---

This is a little note about a function I find myself using frequently in [Clojure](https://clojure.org/).

User interface code often needs to display a list of things as a comma-separated list, e.g. "a, b, c, d".

If all of the things are strings, you can use the built-in `string/join` function to build such a string. When you have a sequence of things that are not strings, I suppose you could convert each element to a string and then use `string/join`. Or you could build your own. I like the function presented below, `convert-seq-to-comma-separated-string`. (Ok, the name needs some work.)

Here's an example of usage.

```Clojure
(defn convert-seq-to-comma-separated-string
  "Return a string containing the members of the sequence separated by commas."
  [the-seq]
  (let [but-last-comma-mapped (map #(str % ", ") (butlast the-seq))]
        (apply str (concat but-last-comma-mapped (str (last the-seq))))))

(defn get-tag-names-for-page
  "Returns a case-insensitive sorted-set of tag names associated with the page.
  If ther are no such tags (it's nil or an empy seq), returns a sorted set
  containing the word 'None'."
  ([page-id]
   (get-tag-names-for-page page-id h2-db))
  ([page-id db]
   (let [tag-ids (get-tag-ids-for-page page-id)]
     (if (or (nil? tag-ids) (empty? tag-ids))
       (into (sorted-set-by case-insensitive-comparator) ["None"])
       (let [tag-ids-as-string (convert-seq-to-comma-separated-string tag-ids)
             sql (str "select tag_name from tags where tag_id in ("
                      tag-ids-as-string ");")
             rs (jdbc/query db [sql])]
         (reduce #(conj %1 (:tag_name %2))
                 (sorted-set-by case-insensitive-comparator) rs))))))
```

In this case, I wanted to build a SQL command that included a list of identifiers, integers in this case. The function has come in very handy for things like this.
