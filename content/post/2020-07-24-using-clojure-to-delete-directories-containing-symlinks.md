---
author: david
title: Using Clojure to Delete Directories Containing Symlinks
date: 2020-07-24T10:17:45.426-04:00
modified: 2020-07-24T10:34:45.088-04:00
tags:
  - Clojure
  - recursion
  - symlinks
---

As part of repository maintenance, I often use a script to delete a group of directories, even if those directories are not empty. It looks something like this:

```clojure
(require '[clojure.java.io :as io])
.
.
.
(defn delete-children-recursively! [f]
  "Given a file, delete it. If the file is a directory delete its
  contents first. This version does not handle symlinks."
  (when (.isDirectory f)
    (doseq [f2 (.listFiles f)]
      (delete-children-recursively! f2)))
  (when (.exists f) (io/delete-file f)))

(defn delete-clean-targets! [v]
  "Given a vector of directory names (strings), delete the directories
   and any files they contain."
  (doseq [dir-name v]
    (let [dir-file (io/file dir-name)]
      (delete-children-recursively! dir-file))))

(delete-clean-targets! clean-targets)
(System/exit 0)
```

Somewhere, `clean-targets` is defined as a vector of directory names. When passed to the `delete-clean-targets!` function, all of those directories and their contents are removed.

But if any of the directories contain a [symlink](https://en.wikipedia.org/wiki/Symbolic_link), it will not be deleted, and the directory where it resides will not be deleted either since it will not be empty.

The `Files` class provides a way to handle things such that the file can be deleted even if it is a symlink. The revised function looks something like this:

```clojure
(import [java.nio.file Files LinkOption Path Paths])
.
.
.
(defn delete-children-recursively! [f]
  "Given a file, delete it. If the file is a directory delete its
  contents first. This version handles symlinks by deleting the link,
  not the file it links to."
  (when (.isDirectory f)
    (doseq [f2 (.listFiles f)]
      (delete-children-recursively! f2)))
  (let [file-name (.getAbsolutePath f)
        fp (Paths/get file-name (into-array String []))]
    (try
      (when (Files/exists fp (into-array [java.nio.file.LinkOption/NOFOLLOW_LINKS]))
        (Files/delete fp))
      (catch Exception e
        (println "Problem trying to delete " fp)
        (println "e: " e)))))
```

There are a couple of interesting changes here, particularly the handling of optional arguments. For interoperability, those arguments get packed up into a native Java array as shown in the call to `Files/exists` with the inclusion of the `NOFOLLOW_LINKS` argument. It is also required when there are no additional argument, such as the call to `Paths/get`.

Note that using the `NOFOLLOW_LINKS` option means that only the symlink will be deleted, not the file that it points at.