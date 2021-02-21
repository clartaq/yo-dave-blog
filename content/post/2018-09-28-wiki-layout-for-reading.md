---
author: david
title: Wiki Layout for Reading
date: 2018-09-23T13:20:23.064-04:00
modified: 2018-09-28T14:09:11.476-04:00
tags:
  - CSS
  - flex box
  - flexbox
  - layout
  - semantic markup
---

​As many of you know, I've been working on a personal wiki for some time now. (See the [CWiki repository](https://helixteamhub.cloud/Regolith/projects/binom-stats/repositories/cwiki/tree/default).) In fact, I'm using it to write this post. It's very comfortable for me.

One of the things I have been trying to get absolutely right (for me at least) is the layout. There are really only two layouts; one is for the "reading" view, the other for the "editing" view.

But getting the "reading" and "editing" views just right has been a challenge. After CWiki became usable, I found a number of friction points that I wanted to correct. Many of the problematic areas were related to which parts of the view are stationary and which can scroll. There were also problems with getting identical behavior with different browsers.

Since the CSS for CWiki is large and ugly (in my opinion), I decided to create a [very simple project](https://helixteamhub.cloud/Regolith/projects/binom-stats/repositories/clajistan/tree/default) that just contains the layout. This project is an example of using a [CSS flexible box](https://en.wikipedia.org/wiki/CSS_flex-box_layout) or "flexbox" layout for a straightforward web page. The main sections are a page header, an aside, and an article display area arranged like so:

```
+----------------------------------------------+
| Window Header                            Nav |
+-------+-----------------+--------------------+
| Aside | Article Header  |                    |
|       +-----------------+--------------------|
|       | Article Content |                    |
|       |                 |                    |
|       |                 |                    |
|       |                 |                    |
|       |                 |                    |
+-------+-----------------+--------------------+
```

The layout fills the page and adjusts its size as the browser resizes.

There is a window header at the top for branding and navigation. It is full width and does not scroll out of view.

The main area of the window contains an "aside" for things like lists of links. The "aside" section vertically scrolls if​ the content is long enough and the window is too short to show it all. It is of fixed width.

To the right is another vertical flexbox that contains a fixed position article header and a section of article content below it. If the content is long enough, it will scroll in the vertical direction. It takes full width, but, since it is designed for reading, limits the line length to about 70 characters since line lengths of between 55 and 70 characters are considered optimal for readers.

The aside and article sections scroll independently of one another.

I've tried to use semantic markup for the pieces, `<header>`, `<main>`, `<aside>`, `<article>`, and `<section>` where it seemed appropriate.​ I can argue myself into different usages, but these seem appropriate at the moment.

The project, linked above, is two files. The first is `index.html`, containing the skeleton of the layout. The second, `styles.css`, is a CSS file containing the styles used to lay things out as desired. There is no JavaScript needed in this simple layout.

The layout has been tested to perform as explained on the Safari, Firefox, Opera, and Brave browsers. Firefox was the browser that sometimes produced inconsistent behavior.

Experimenting with this layout is much easier than working with the CSS of the CWiki project. I am slowly migrating my findings from the experimental project to CWiki.

Next comes something similar for the "editing" view.