---
author: david
title: Wiki Layout for Editing
date: 2018-10-23T16:05:29.015-04:00
modified: 2018-12-15T10:36:05.964-05:00
tags:
  - CSS
  - editor
  - layout
---

**Update: 2018-12-15** This post substantially updates the original. The most significant changes are that the layout now works as expected - no partial success.

---

A few weeks ago, I wrote a [post about a wiki layout for reading](https://yo-dave.com/2018/09/23/wiki-layout-for-reading/). I used that project to experiment with improvements to the reading view of [CWiki](https://helixteamhub.cloud/Regolith/projects/binom-stats/repositories/cwiki/tree/default), a personal wiki that I am writing. I used the lessons learned in the [project](https://bitbucket.org/David_Clark/wiki-layout/src/default/) described in that post to make some changes to CWiki.

In this post, I describe a layout for editing. Just like last time, I created a [simple project](https://helixteamhub.cloud/Regolith/projects/binom-stats/repositories/wiki-editor-layout/tree/default) to experiment with. The page is laid out like this:

```
+----------------------------------------------------------------+
| Window Header                                              Nav |
+-------+--------------------------------------------------------+
| Aside | +----------------------------------------------------+ | 
|       | | Outer Editor Container                             | |
|       | | +------------------------------------------------+ | |
|       | | | Inner Editor Container                         | | |
|       | | | +--------------------------------------------+ | | |
|       | | | | Editor Header (Title and Tags)             | | | |
|       | | | +--------------------------------------------+ | | |
|       | | | | Editor and Preview Section                 | | | |
|       | | | | +----------------------------------------+ | | | |
|       | | | | | Editor Button Bar                      | | | | | 
|       | | | | +-------------------+--------------------+ | | | |
|       | | | | | Editor Container | Preview Container   | | | | |
|       | | | | | +----------------+-------------------+ | | | | |
|       | | | | | | "Markdown" Label | "Preview" Label | | | | | |
|       | | | | | +------------------+-----------------| | | | | |
|       | | | | | | Editor Pane      | Preview Pane    | | | | | |
|       | | | | | |                  |                 | | | | | |
|       | | | | | |                  |                 | | | | | |
|       | | | | | |                  |                 | | | | | |
|       | | | | | |                  |                 | | | | | |
|       | | | | | +------------------|-----------------+ | | | | |
|       | | | | +----------------------------------------+ | | | |
|       | | | | Bottom Button Bar                          | | | |  
|       | | | +--------------------------------------------+ | | |
|       | | +------------------------------------------------+ | |
|       | +----------------------------------------------------+ | 
+-------+--------------------------------------------------------+
```

This diagram is simplified​ but shows the important parts. Of course, refer to the actual CSS and HTML files.

The "Window Header," "Nav," and "Aside" are identical to the those used in the wiki-layout; the "Article Content" has been replaced with the "Outer Editor Container." These sections should continue to perform just as in the wiki layout. The "Outer Editor Container" is part of the Clojure layout of the page.

Within the "Outer Editor Container" is another container, the "Inner Editor Container," that is created by the ClojureScript editor functionality when the user elects to edit or create a page.

Within the "Inner Editor Container" there is a:

* **Editor Header** that contains an editable title and editable tags and perhaps, in the future, some other information.
* **Editor and Preview Section** holding an **Editor Button Bar**, **Editor Container** and **Preview Container**. The "Editor Button Bar" contains buttons to perform some simple formatting.
   The "Editor Container" and "Preview Container" each contain a label at the top and a "Pane" of information.
* **Editor Pane** where editing of the main content occurs.
* **Preview Pane** that shows a real-time preview of the main content as it is entered and modified.
* At the bottom is another button bar, similar to that used in other parts of the program. It contains a single button: "Done" used to exit the editor and return to the main page view.

The "Editor Pane" and "Preview Pane" should expand to fill all of the remaining vertical space not taken up by the other components.
Once laid out, the "Editor Pane" section should not be re-sizeable.

The "Editor Header" should never scroll no matter how long the contents of the "Editor Pane" or "Preview Pane" are.

The "Preview Pane" is similar to the "Editor Pane" but is not editable. It may be of different length since the layout of the final contents of the preview may be longer or shorter than the "Editor Pane."

With this page layout​, it should only be possible to scroll the "Aside," "Editor Pane" and "Preview Pane" if their content is too long to fit in the space laid out for them. None of the other parts of the page should be scrollable.

The "Editor Pane" and "Preview Pane" should scroll independently. Perhaps someday the scrolling between these two sections will by synchronized programmatically.

The layout has been tested to perform as explained on [Safari](https://www.apple.com/safari/), [Firefox](https://www.mozilla.org/en-US/firefox/), [Opera](https://www.opera.com), and [Brave](https://brave.com) browsers.

## Implementation in CWiki ##

The initial transfer of the CSS governing this layout to CWiki was not entirely successful. It was possible to "pull" and "push" things such that part of the "Window Header" would scroll out of view and it was never possible to get the bottom button entirely​ in the viewport.

It turned out that the `box-sizing` attribute of some components​ was not set correctly. Setting it to `box-sizing: box-border` was the last piece of the puzzle. Since making the change, things have been working flawlessly.