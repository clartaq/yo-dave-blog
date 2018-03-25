---
title: ClojureScript with Reagent and figwheel
date: 2016-03-07
modified: 2018-03-17T09:54:48.159604-04:00
tags:
  - clojurescript
  - figwheel
  - reagent
  - web development
categories:
  - programming
---

I have a bit of a love/hate relationship with [ClojureScript](https://github.com/clojure/clojurescript). On the one hand, it is a Lisp, with all the power it entails. On the other, the development toolchain can be byzantine. With the advent of [WebAssembly](http://www.2ality.com/2015/06/web-assembly.html) and [ECMAScript6](https://github.com/lukehoban/es6features/blob/master/README.md), I have hopes of seeing tail call optimization (allowing true recursion) handled in ClojureScript, if not Clojure itself. And [Reagent](https://github.com/reagent-project/reagent) and [figwheel](https://github.com/bhauman/lein-figwheel) can make web development (not my strong suite) much easier.

<!--more-->

Reagent is a ClojureScript library wrapped around the [Reactjs](https://facebook.github.io/react/) tool from Facebook. As with React, it is intended to handle the creation and rendering of the UI for your applications.

figwheel is a plugin for the Leiningen build tool used by many Clojure(Script) projects. It automatically updates your browser when you make changes to the source code of the app enabling you to see almost instantly the effects of those changes.

Here I talk about how easy it is to use ClojureScript with Reagent and figwheel to do some simple web development.

## You need to install Java ##

You need Java to run part of the toolchain. As far as I know, you don’t need it for deploying your application. There are plenty of guides for installing Java. I won’t repeat them here. During the development of the code described here, I used version 1.8.0_74, 64-bit.

## Install Leiningen ##

If you aren’t already familiar with it, [Leiningen](http://leiningen.org/) is the de-facto Clojure(Script) build tool. There are simple installation instructions on the project’s home page. I used version 2.6.1.

## Create a Project ##

That’s pretty much all the installation work you have to do. Leiningen along with your project file will take care of the rest. “Project File?” you ask? Yes, it’s created by Leiningen too. The good folks here have created a project template that can set up a skeleton project for you. Just type the following to move to your project directory (wherever it might be — I have a `c:\projects` directory on my system) and create a new project.

```dos
C:\Users\david>cd \projects
C:\projects>lein new reagent-figwheel rgt-fw-demo
```

This will create a new directory, `rgt-fw-demo`, with a skeleton project of the same name all laid out. After the command completes, you can change to the new directory and run the program.

```dos
C:\projects>cd rgt-fw-demo
C:\projects\rgt-fw-demo>lein figwheel
```

After a short delay, you should see a response like the following on your terminal.

```dos
Figwheel: Starting server at http://localhost:3449
Figwheel: Watching build - dev
Compiling "resources/public/js/compiled/app.js" from ["src/cljs"]...
Successfully compiled "resources/public/js/compiled/app.js" in 3.0 seconds.
Launching ClojureScript REPL for build: dev
Figwheel Controls:
          (stop-autobuild)                ;; stops Figwheel autobuilder
          (start-autobuild [id ...])      ;; starts autobuilder focused on optional ids
          (switch-to-build id ...)        ;; switches autobuilder to different build
          (reset-autobuild)               ;; stops, cleans, and starts autobuilder
          (reload-config)                 ;; reloads build config and resets autobuild
          (build-once [id ...])           ;; builds source one time
          (clean-builds [id ..])          ;; deletes compiled cljs target files
          (print-config [id ...])         ;; prints out build configurations
          (fig-status)                    ;; displays current state of system
  Switch REPL build focus:
          :cljs/quit                      ;; allows you to switch REPL to another build
    Docs: (doc function-name-here)
    Exit: Control+C or :cljs/quit
 Results: Stored in vars *1, *2, *3, *e holds last exception object
Prompt will show when Figwheel connects to your application
To quit, type: :cljs/quit
cljs.user=>
```

We’ll talk about some of the details later, but if you open a browser and navigate to http://localhost:3449, you should see this output:

[![](https://github.com/clartaq/yo-dave/raw/master/images/2016-03-07-rgt-fw-hello-snip.PNG)](https://github.com/clartaq/yo-dave/blob/master/images/2016-03-07-rgt-fw-hello-snip.PNG)

And, indeed we shall “FIXME” right now!

## Update and Modify project.clj ##

In the editor of your choice, open the project.clj file. On my system, several of the dependencies were out of date, and I included some additional information sections. Make your file look like the following. Take special note of the last line.

```clojure
(defproject rgt-fw-demo "0.1.1-SNAPSHOT"
    :description "A small demo using Reagent and figwheel in Clojurescript"

    :url "http://example.org/sample-clojure-project"

    :license {:name "The MIT License"
              :url "https://opensource.org/licenses/MIT"
              :distribution :repo}

    :dependencies [[org.clojure/clojure "1.8.0"]
                   [org.clojure/clojurescript "1.9.229"]
                   [reagent "0.6.0"]]

    :min-lein-version "2.5.3"

    :plugins [[lein-cljsbuild "1.1.4"]
              [lein-figwheel "0.5.7"]]

    :clean-targets ^{:protect false} ["resources/public/js/compiled" "target"]

    :cljsbuild {:builds [{:id "dev"
                          :source-paths ["src/cljs"]
                          :figwheel {:on-jsload "rgt-fw-demo.core/main"}
                          :compiler {:main rgt-fw-demo.core
                                     :output-to "resources/public/js/compiled/app.js"
                                     :output-dir "resources/public/js/compiled/out"
                                     :asset-path "js/compiled/out"
                                     :source-map-timestamp true}}

                         {:id "min"
                          :source-paths ["src/cljs"]
                          :compiler {:main rgt-fw-demo.core
                                     :output-to "resources/public/js/compiled/app.js"
                                     :optimizations :advanced
                                     :pretty-print false}}]}

    :figwheel {:css-dirs ["resources/public/css"]})
```

Normally, figwheel watches the source code files in your project for changes, as it notices changes in you ClojureScript files, the project is rebuilt and any relevant changes are pushed out to the browser without you having to stop the program, re-compile, launch, and refresh the browser. It's a big time saver. It turns out that figwheel can do something similar when the CSS in your project changes too. The content of the last line, `:figwheel {:css-dirs ["resources/public/css"]}`, tells figwheel where there are CSS files to monitor.

## Add some CSS ##

By default, the project template doesn't create any CSS, but it's easy to do. First create a directory where we told figwheel to look.

```dos
C:\projects\rgt-fw-demo>mkdir resources\public\css
```

Now, create the file style.css and fill it with the following contents. Save the file.

```css
h1,h2,h3,h4,h5,h6 {
    background-color: transparent;
    color: dimgray;
    font-family: "Segoe UI", "Lucida Grande", Tahoma, Verdana, Helvetica,
                    Arial, sans-serif;
}

body {
    font-family: "Segoe UI", "Lucida Grande", Tahoma, Verdana, Helvetica,
                    Arial, sans-serif;
    background-color: white;
    color: rgb(70, 70, 70);
    font-size: 0.95em;
}

#footer{
    margin-bottom:30px;
    font-size:0.825em;
    color: silver;
}

#left-column {
    width: 200px;
    position: fixed;
}

#right-column {
    float: right;
    text-align: right
}
```

And speaking of things webby, let's polish up the HTML a bit. Open the `resources\public\index.html` file and make it look like this.

```html
<!DOCTYPE html>
<html lang="en-us">
    <head>
        <meta charset="UTF-8">
        <link rel="stylesheet" href="css/style.css">
        <title>rgt-fw-demo</title>
    </head>
    <body>
        <div id="app"></div>
        <script src="js/compiled/app.js"></script>
        <script>rgt_fw_demo.core.main();</script>
    </body>
</html>
```

These changes make clear to browsers what language we are using (US English), what character set to use (UTF-8) and provide a link to the CSS style sheet we just added to the project. After saving these changes to HTML and CSS, you can reload the page in the browser and see the following:

[![](https://github.com/clartaq/yo-dave/raw/master/images/2016-03-07-rgt-fw-hello-snip2.PNG)](https://github.com/clartaq/yo-dave/blob/master/images/2016-03-07-rgt-fw-hello-snip2.PNG)

The text is still un-inspiring, but now it is in a snazzy sans-serif font. Woohoo!

## Finally, Some Interesting ClojureScript Stuff ##

With all of the preparatory work complete at last, we can do some work in ClojureScript.

This demo will consist of making changes to a single file, `src/cljs/rgt_fw_demo/core.cljs`. Initially, it looks like this:

```clojure
(ns rgt-fw-demo.core
    (:require [reagent.core :as reagent]))

(defonce app-state (reagent/atom {:text "Hello, what is your name? "}))

(defn page []
  [:div (@app-state :text) "FIXME"])

(defn ^:export main []
  (reagent/render [page]
                  (.getElementById js/document "app")))
```

There is a single additional namespace `:require`d by the program, `reagent.core`. Here it is aliased as `reagent`, but we will shortly change that to the much shorter `r`.

The `app-state Var` contains some program state as a `Reagent` version of an `atom`. In Reagent, an `atom` is similar to a Clojure `atom` except that the framework monitors the created `Var` for changes during program execution. If `app-state` changes programmatically, any view components related to it will change too. If you change the value in your editor and save the file, it will look similar, but figwheel makes that change. We'll try to make a clearer example in a moment.

Next comes the definition of the `page` function. The `page` function creates what is called a <em>component</em> in Reagent parlance. A component is a self-contained thing that can be rendered to a web page. In this case, it will display the contents of `app-state` followed by the text "FIXME".

The `main` function triggers rendering of the component specified (`page` in this case) into the DOM object called "app". If you check the `index.html` file above, you will see just such an element. It's a `div` with the id "app".

The `main` function will be largely unchanged in this demo. For now, change the reference to the `page` component to `home-page`. While you're at it, change the alias for reagent.core in the :require function to r. Change the usage in main too. Then delete the definitions for `app-space` and the `page`> function.

Okay, we changed the `main` function to render a new component called `home-page`. Let's create it now. It's pretty simple.

```clojure
(defn home-page []
  [:div
   [header-component]
   [body-component]
   [footer-component]])
```

This function creates a new `div` and places three additional components in it. Notice that the `div` and its contents are described with a [Hiccup](https://github.com/weavejester/hiccup)-like syntax. This is a way to represent HTML in Clojure(Script). I actually prefer it to HTML.

Notice that the three sub-components are not included as function calls (although they are created with functions, as we shall see.) Components can be composed of sub-components to any depth.

### Create a Header Component ###

Let's create the first sub-component, `header-component`, by defining a `header-component` function.

```clojure
(defn header-component []
  [:div
   [:h1 "Reasons to Love "
    [:a {:href "https://github.com/reagent-project/reagent"} "Reagent"]]])
```

Again we see the use of Hiccup-like syntax to build up another component. We see a new `div` and an `h1` header with text that includes a link to the Reagent project. If we were able to run the project right now, it would look like:

[![](https://github.com/clartaq/yo-dave/raw/master/images/2016-03-07-rgt-fw-demo-title.PNG)](https://github.com/clartaq/yo-dave/blob/master/images/2016-03-07-rgt-fw-demo-title.PNG)

### A Two-Column Footer ###

Let's try something a little more complicated. Let's create a footer that contains two columns with the right-most column displaying the program version. First, we need the program version. Start by adding the following just below the namespace declaration.

```clojure
(def major-version 0)
(def minor-version 1)
(def patch-version 1)
(def program-version (str "v" major-version "." minor-version "." patch-version))
```

This just creates a string containing a program version number in [semantic](http://semver.org/) form. Placement of these `def`s isn't really critical, I just prefer them in this location. You can put it wherever you want as long as it compiles.

Now add a function/component to render the footer.

```clojure
(defn footer-component []
  [:div#footer
   [:div#left-column
    [:p "This is a two-column footer with some more complicated CSS behind it that shows the"
     " program version right-justified."]]
   [:div#right-column
    [:p program-version]]])
```

If you could run the program now, you would see a footer on your page that looked like this:

[![](https://github.com/clartaq/yo-dave/raw/master/images/2016-03-07-rgt-fw-demo-footer.PNG)](https://github.com/clartaq/yo-dave/blob/master/images/2016-03-07-rgt-fw-demo-footer.PNG)

### Create a Body Component ###

We've referred to a `body` component in `home-page`. Let's create it.

```clojure
(defn body-component []
  [:div
   [:p "Well..."]
    [:ul
     [:li "It's easy. ("
      [:a {:href "https://github.com/weavejester/hiccup/blob/master/project.clj"} "Hiccup"]
      "-like syntax is very intuitive.)"]
     [:li "It's fast. (Both development and performance.)"]
     [:li "I'm a chemist"]
     [:li "and..."]]]
   [:p "It can tell time! " [:b [clock]]])
```

Now we have a list of our favorite things about Reagent, but the program still won't run because of the reference to the `clock` component yet to be defined. Do it like this:

```clojure
(defonce timer (r/atom (js/Date.)))

(defonce time-updater (js/setInterval
                        #(reset! timer (js/Date.)) 1000))

(defn clock []
  (let [time-str (-> @timer
                     .toTimeString
                     (clojure.string/split " ")
                     first)]
    [:span time-str]))
```

This snippet starts with the declaration of the `timer atom`. But this is no vanilla Clojure atom; it's a Reagent atom. The magic here is that Reagent monitors changes to these atoms and causes components that use them to be re-rendered.

The `timer-updater Var` starts up an anonymous function that updates the `timer atom` every second. Even though it updates the value of timer, it doesn't <em>do</em> anything with it.

The `clock` function creates a component that <em>does</em> use the value of `timer`. Not only that, because `timer` is a Reagent atom, the function is re-rendered every time `timer` changes value, that is every second. So the component includes a dynamically updated display of the time.

The home-page should look something like this in your browser:

[![](https://github.com/clartaq/yo-dave/raw/master/images/2016-03-07-rgt-fw-demo-home-page.PNG)](2016-03-07-rgt-fw-demo-home-page.PNG)

You should see your actual time that continually updates. All of the machinery for updating the view as the program state changes is out of site in the Reagent/React internals. We get the goodness for almost nothing.

The `Var`s containing program state can be arbitrarily complex and there can be any number of them. You can treat them just like regular ol' Clojure atoms but any components that use that state get updated as it changes. All for almost free.

It's like magic.

The complete project is available as a [Mercurial](https://www.mercurial-scm.org/) repository on [BitBucket](https://bitbucket.org/David_Clark/rgt-fw-demo).

**Update 2016-09-25**: Fixed a bug in the HTML where a `<ul>` element was embedded inside a `<p>` element. Updated dependencies in the project file. Updated the version number in the project and source file (although it is not shown in the copy of the screen shot.)