---
author: david
title: Writing a flexmark Extension in Clojure
date: 2018-09-16T10:10:08.069-04:00
modified: 2018-09-17T10:23:41.474-04:00
tags:
  - Clojure
  - flexmark
  - Markdown
---

Writing [wiki](https://en.wikipedia.org/wiki/Wiki) pages using [Markdown](https://daringfireball.net/projects/markdown/) is a great way to do things because it is simple, well-known, and capable.

One of the annoyances of using Markdown is that it has no default syntax to create [wikilinks](https://en.wiktionary.org/wiki/wikilink), a type of [hyperlink](https://en.wiktionary.org/wiki/hyperlink) that links one page in a wiki to another.

I've been working on a home-grown, personal wiki, [CWiki](https://bitbucket.org/David_Clark/cwiki/src/default/), for almost a year now off and on. It's written in the [Clojure](https://clojure.org) programming language, which is a pleasure to use. One of Clojure's strengths is the excellent interoperability that it has with the [Java](https://en.wikipedia.org/wiki/Java_(programming_language)) ecosystem.

The CWiki program translates wiki pages written in the Markdown language and to HTML for display on a web page. It uses a Java library called [flexmark-java](https://github.com/vsch/flexmark-java) (or simple "flexmark") to do the translation. The flexmark library is very well-organized and fast. It is also extremely configurable; it adheres to the [CommonMark](https://commonmark.org) specification by default but is capable of emulating the features of many different version of Markdown, such as [GFM](https://github.github.com/gfm/) (GitHub-Flavored Markdown). The flexmark library achieves its flexible configuration with the use of extensions.

CWiki attempts to emulate the wikilink style of [MediaWiki](https://www.mediawiki.org/wiki/MediaWiki). Initially, I just scanned documents for wikilinks using a regular expression approach. Whenever the regular expression matched a wikilink, it was converted to an HTML link before the material was converted from Markdown. This naive approach worked well most for most content, but in the case of wikilinks embedded in code listings, it would expand the link within the code listing. That made writing examples of wikilink usage problematic, so I wanted a solution where the Markdown was parsed rather than matched to a regular expression. The parser would know about when it was dealing with a code listing or a real wikilink.

Thankfully, there is already a flexmark extension to handle wikilinks, but it isn't quite what I want. One of the key features that I want to follow is coloring links to non-existent pages red instead of the standard link color. But the pre-written extension to flexmark doesn't do that. 

## An AttributeProvider to Style Links ##

Time to write an extension. As I mentioned, flexmark is a very flexible Java library. Creating the extension I want requires writing _two_ additional Java classes. One of the classes will take care of styling the link; the other will handle hooking the extension into the flexmark library.

Styling links in the manner I want can be accomplished by writing an [`AttributeProvider`](https://github.com/vsch/flexmark-java/blob/master/flexmark/src/main/java/com/vladsch/flexmark/html/AttributeProvider.java). The `AttributeProvider` is an `interface` with a single method that must be implemented by any class extending it,

    void setAttributes(Node node, AttributablePart part, Attributes attributes);

Also, the implementing class should provide a static factory method to create one of these things. I called my extension class a `WikiLinkAttributeProvider.` Here's how it looks.

```
;-------------------------------------------------------------------------------
; The WikiLinkAttributeProvider is responsible for styling the links
; depending on whether the page exists (normal link), doesn't exist (red link),
; and whether the user is even authorized to edit/create a new page (disabled
; link, depending on user permissions.)
;-------------------------------------------------------------------------------

(gen-class
  :name cwiki.util.WikiLinkAttributeProvider
  :implements [com.vladsch.flexmark.html.AttributeProvider]
  :methods [^{:static true} [Factory [] com.vladsch.flexmark.html.AttributeProviderFactory]])

(defn -setAttributes
  "Style the wikilink dependent on the user role and page existence."
  [this ^Node node ^AttributablePart part ^Attributes attributes]
  (when (= (type node) WikiLink)
    (let [title (str (.getLink ^WikiLink node))
          style-to-use (title->link-style title (ri/retrieve-session-info))]
      (when style-to-use
        (.replaceValue attributes "style" style-to-use)))))

(defn ^AttributeProviderFactory -Factory []
  (proxy [IndependentAttributeProviderFactory] []
    (create [^LinkResolverContext context]
      (cwiki.util.WikiLinkAttributeProvider.))))

```
(I've left out a lot of other code, like the imports and utility functions. A [gist](https://help.github.com/articles/about-gists/) with the complete code listing for the two classes in the extension and some test code is [here](https://gist.github.com/clartaq/18b03db43c04d8956e0410ed6104dd27) or you can check out the project repository (which might change with further development).)

The `gen-class` macro is used to create the Java class with the given name. The macro states that the class will implement the interface and will provide an additional method, the static factory method.

The `setAttributes` method looks at the type of `Node` that is passed to it and, if the node is a `WikiLink`, figures out how to style it. The `title->link-style` function makes that determination based on whether a page with the given title already exists and, if not, whether the user has a role that allows them to create new pages. (If the page does not exist, a user with sufficient permissions can create it by clicking the link and editing it. Otherwise,​ the link is disabled.)

The static factory method, `create`, calls the ​constructor for the class that is automatically generated by the `gen-class` macro.

## An HTMLRendererExtension to Connect to flexmark ##

Well, that was pretty easy. Now, to connect the `AttributeProvider` to flexmark, we need to create an `HTMLRendererExtension`. This is an internal class within​ the [`HTMLRenderer`](https://github.com/vsch/flexmark-java/blob/master/flexmark/src/main/java/com/vladsch/flexmark/html/HtmlRenderer.java) class that extends the `Extension` interface.

This class too is fairly simple, but getting it to work was a little more difficult. Here is what my extension looks like in Clojure.

```
;-------------------------------------------------------------------------------
; The WikiLinkAttributeExtension provides the glue that plugs the
; WikiLinkProvider into the flexmark wikilink extension.
;-------------------------------------------------------------------------------

; First provide a forward declaration.
(gen-class
  :name cwiki.util.WikiLinkAttributeExtension
  :implements [com.vladsch.flexmark.html.HtmlRenderer$HtmlRendererExtension])

; Then expand it with the declaration of the method that returns an
; instance of the class.
(gen-class
  :name cwiki.util.WikiLinkAttributeExtension
  :implements [com.vladsch.flexmark.html.HtmlRenderer$HtmlRendererExtension]
  :methods [^{:static true} [create [] cwiki.util.WikiLinkAttributeExtension]])

(defn -rendererOptions
  [this ^MutableDataHolder options]
  ; Add any configuration option that you want to apply to everything here.
  )

(defn -extend
  [this ^HtmlRenderer$Builder rendererBuilder ^String rendererType]
  (.attributeProviderFactory rendererBuilder (cwiki.util.WikiLinkAttributeProvider/Factory)))

(defn ^cwiki.util.WikiLinkAttributeExtension -create []
  (cwiki.util.WikiLinkAttributeExtension.))
```

Really, all this class has to do is provide factory methods for itself and the `WikiLinkAttributeProvider`. But getting that to work was a bit of trial and error. A single `gen-class` would not work. A reply to a [question](https://stackoverflow.com/questions/51969711/creating-a-flexmark-extension-in-clojure) on StackOverflow suggested using two calls to `gen-class` where the first acted as a type of "forward" declaration. It still is not obvious to me why this is required, but it works.

## Other Things to Know ##

### Importing the Classes ###

Since the code for the extension creates Java classes, it has to be `import`ed, not `require`ed or `used`. The namespace declaration where I use these classes looks something like this.

```
(ns cwiki.layouts.base
  (:require ... )
  (:import (cwiki.util WikiLinkAttributeExtension)...))
```

### Setting the Options and Instantiating the Parser and Renderer ###
Adding the extension into the rest of the program is straightforward. It works just like adding any other extension to flexmark. Here is how I initialize flexmark and create a function to translate my flavor of Markdown to HTML.

```
(def options (-> (MutableDataSet.)
                 (.set Parser/REFERENCES_KEEP KeepType/LAST)
                 ...     
                 (.set TablesExtension/HEADER_SEPARATOR_COLUMN_MATCH true)
                 (.set WikiLinkExtension/LINK_FIRST_SYNTAX true)
                 (.set WikiLinkExtension/LINK_ESCAPE_CHARS "")
                 (.set Parser/EXTENSIONS (ArrayList.
                                           [...
                                            (WikiLinkExtension/create)
                                            (WikiLinkAttributeExtension/create)
                                            (TablesExtension/create)]))))

(def parser (.build ^com.vladsch.flexmark.parser.Parser$Builder (Parser/builder options)))
(def renderer (.build ^com.vladsch.flexmark.html.HtmlRenderer$Builder (HtmlRenderer/builder options)))

(defn- convert-markdown-to-html
  "Convert the markdown formatted input string to html
  and return it."
  [mkdn]
  (->> mkdn
       (.parse parser)
       (.render renderer)))
```
### Order of Compiling Things ###

I use [Leiningen](https://leiningen.org) as the build tool for most of my Clojure projects. In the usual workflow, Leiningen will build Java source files first, then Clojure files. I presume the thinking is that the Clojure files will import the classes produced from any Java files in the project.

But there are no Java files in the extension and some of the Clojure files still need to import the extension classes. Leiningen provides a simple way to control the order of compilation using the `:prep-tasks` key in the project file.

Here's the line I include in my `:dev` profile.

```
:prep-tasks [["compile" "cwiki.util.wikilink-attributes"]]
```

### One Final Rough Edge to Mention ###

I use the [Cursive](https://cursive-ide.com) plugin for [IntelliJ IDEA](https://www.jetbrains.com/idea/) to develop in Clojure. I've gotten used to running a REPL in Cursive and using the keyboard shortcuts to run tests while in the editor. However, this is not so automatic for this project. It requires that the extension classes be compiled manually before the tests will run. Not a big thing, but not quite perfect.