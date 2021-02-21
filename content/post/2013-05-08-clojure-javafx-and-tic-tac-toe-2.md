---
title: 'Clojure, JavaFX and Tic-Tac-Toe'
tags:
  - clojure
  - javafx
id: 595
comment: false
categories:
  - programming
date: 2013-05-08 17:20:37
modified: 2018-03-25T15:53:51.054106-04:00
---

Recently, I have been experimenting with [JavaFX](http://www.oracle.com/technetwork/java/javafx/overview/index.html "Link to JavaFX developer") in [Clojure](http://clojure.org/ "Link to the Clojure language home page."). Initially, in one of my experiments, I wanted to learn how to re-size a game-board interface as it's containing window was re-sized. In the past I've had medical device interfaces that draw a representation of a physical device and these drawings must re-size as their window is re-sized. The [initial experiment](https://clartaq.github.io/yo-dave/2013/02/18/2013-02-18-re-sizing-an-interface-in-javafx-and-clojure/) was with a simple interface for Tic-Tac-Toe. Since I had such a nice interface, I thought, why not program the complete game.

<!--more-->

Well, it's done. The code is [here](https://bitbucket.org/David_Clark/tic-tac-toe). It's unbeatable. If you happen to win, it's a bug.

[![An In-Progress Tic-Tac-Toe Game](/static/img/2013-05-08-PlaySnip.png)<br><small>An in-progress Tic-Tac-Toe game</small>](/static/img/2013-05-08-PlaySnip.png)

The key to re-sizing is to watch the width and height properties of the window containing the board and attach change listeners to the properties. The relevant code from the program:

```clojure
    ...
    (doto (.widthProperty canvas)
      (.bind (.widthProperty center))
      (.addListener
        (proxy [ChangeListener] []
          (changed [ov old-state new-state]
            (redraw-board canvas)))))
    (doto (.heightProperty canvas)
      (.bind (.heightProperty center))
      (.addListener
        (proxy [ChangeListener] []
          (changed [ov old-state new-state]
            (redraw-board canvas)))))
    ...
```

Every time the canvas is re-sized, the `redraw-board` function is called. That function takes care of calculating the new size of the board and symbols, then draws them.

Another feature of JavaFX is the ability to style the interface elements. Since flat interfaces are all the rage now, I thought I would play with that too. This aspect of JavaFX still feels a little unfinished in that some styling can be accomplished programmatically while other styles can only be done with CSS. For example, note the "Reset" button in the screen capture above. You can set some of the style directly in the program. To get all of the behavior you want for the button, such as mouse-over handling, you have to use a CSS file. Here's the file I used for the program.

```css
    /********************************************************************************
    *                                                                               *
    * Push Button - Styles stolen from Pedro Duque Vieira a.k.a. "Pixel Duke".      *
    * See his post at                                                               *
    * http://pixelduke.wordpress.com/2012/10/23/jmetro-windows-8-controls-on-java/  *
    *                                                                               *
    ********************************************************************************/

    .root {
        -fx-background-color: #fafad2;
    }

    .button {
        -fx-padding: 5 22 5 22;
        -fx-border-style: null;
        -fx-background-radius: 0;
    
        -fx-background-color: #cccccc;
    
        -fx-font-family: "Segoe UI", Helvetica, Arial, sans-serif;
        -fx-font-size: 11pt;
        -fx-text-fill: black;
    }

    .button:hover {
        -fx-background-color: #d8d8d8;
    }

    .button:pressed, .button:default:hover:pressed {
        -fx-background-color: black;
        -fx-text-fill: white;
    }
    
    .button:focused {
        -fx-border-color: black;
        -fx-border-width: 1;
        -fx-border-style: segments(1, 1);
        -fx-background-insets: 0 0 0 0, 0, 1, 2;
    }

    .button:disabled, .button:default:disabled {
        -fx-opacity: 0.4;
        -fx-background-color: #cccccc;
        -fx-text-fill: #212121;
    }
    
    .button:default {
        -fx-background-color: #40e0d0;
        -fx-text-fill: #ffffff;
    }
    
    .button:default:hover {
        -fx-background-color: #48d1cc;
    }
```

I've[ already written](https://yo-dave.com/2013/05/08/styled-dialogs-in-javafx-with-jfxtras-monologfx/) about achieving similarly styled dialogs in [MonologFX](https://blogs.oracle.com/javajungle/entry/monologfx_floss_javafx_dialogs_for "Link to MonologFX introduction blog.").

One of the nice things about writing a program like this in Clojure is the way listener-type methods can be written. This program has a few. Some `ChangeListener`s were shown above. There are also a couple of `EventListener`s for button clicks. In fact, most of the game play occurs in the method that listens for player clicks on the game board.

```clojure 
    ...
    (defn canvas-click-handler
      "Handle a click on the board canvas. If the click is within the bounds of the
       board, interpret it as an attempt by the human player to place a symbol on
       the board. If the click is on an empty position, place the symbol and get
       the computer's response. If the game is over after either the human or
       computer plays, declare the winner or draw and offer to start a new game."
      [canvas]
      (reify EventHandler
        (handle [this event]
          (let [x (.getSceneX event)
                y (.getSceneY event)]
            (if (click-is-in-bounds? x y)
              (let [pos (click-pos-&gt;board-pos x y)]
                (when (square-is-empty? @position pos)
                  (fill-square pos @me)
                  (redraw-board canvas)
                  (if (logic/game-is-over? @position)
                    (declare-winner canvas)
                    (let [square (get-opponent-move @position @me)]
                      (fill-square square (logic/opponent @me))
                      (redraw-board canvas)
                      (if (logic/game-is-over? @position)
                        (declare-winner canvas)))))))))))
    ...
```

This method still feels like there is some room for improvement, but it works for now.

The computer player is closely derived from the one described in Chapter 10 of "Simply Scheme" by Brian Harvey and Matthew Wright. The unit tests in the project consist mostly of the example function usage shown in that chapter. The book is available in several forms [on-line](http://www.eecs.berkeley.edu/~bh/ssch10/ttt.html "Link to on-line version of Chapter 10 from Simply Scheme."). It is unbeatable. If you happen to beat the game, it is undoubtedly a program bug introduced in my translation.

If you are interested in the complete code for the project, it is on HelixTeamHub [here](https://helixteamhub.cloud/Regolith/projects/binom-stats/repositories/tic-tac-toe/tree/default"Link to Tic-Tac-Toe project repository.").