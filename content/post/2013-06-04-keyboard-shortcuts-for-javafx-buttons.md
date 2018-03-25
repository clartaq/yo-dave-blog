---
title: Keyboard Shortcuts for JavaFX Buttons
tags:
  - clojure
  - java
  - javafx
  - swing
id: 647
comment: false
categories:
  - programming
date: 2013-06-04 04:24:09
---

Most programs written for graphical user interfaces still provide a way to operate with the keyboard, requiring minimal mouse usage. The thought is that expert users will want to speed through their work keeping their fingers on the keyboard rather than devote an entire hands worth of fingers to controlling the mouse. I've been learning [JavaFX](http://www.oracle.com/technetwork/java/javafx/overview/index.html), the eventual replacement for the [Swing](http://en.wikipedia.org/wiki/Swing_%28Java%29) UI framework on [Java](http://en.wikipedia.org/wiki/Java_%28programming_language%29), and wanted to explore how shortcut functionality had changed. There were a few tutorials on keyboard shortcuts for menu-driven programs, but nothing I could find on their use with button-based interfaces. That's what I cover here.

# Nomenclature #

There are really two groups of these shortcuts. The first group, _keyboard shortcuts_, are also called _keyboard accelerators_. They tend to be global. For example, most Windows programs allow you to save a file by simultaneously pressing the `Control` key and the `S` key, usually denoted `Ctrl-S`. (On Mac, the `Ctrl` key is usually replaced by the `Command` key.) You don't have to have a menu open for this to work.

The second group, _keyboard mnemonics_, only operate when the control element in question is visible and are activated with the `Alt` key plus a character key. For example, on most Windows programs the keyboard sequence `Alt-H`/`Alt-A` will display an "About" dialog. The sequence `Alt-E`/`Alt-A` will "select all". The program behavior produced by pressing `Alt-A` will depend on which menu is displayed.

With mnemonics, you _navigate_ the menu system. With shortcuts, you _bypass_ the menu system. If you are using menus.

I will try to use the terms "shortcut" for keyboard accelerator and "mnemonic" for keyboard mnemonic consistently from this point on.

# Mnemonics #

Setting up keyboard mnemonics is pretty straightforward in JavaFX. First, call the `setMnemonicParsing()` method for the button with an argument of true. Then, when you set the text to be displayed on the button, prefix the character to be used as the mnemonic with an underline. For example, to display the mnemonic
"<u>F</u>ile" set the text to "_File".

It doesn't seem to make any difference if you call `setMnemonicParsing(true)` and you don't provide the underline in the label text. So it isn't clear to me why the function is needed at all. Just enable the parsing by default and ignore labels that don't have any underline. Maybe it is there to provide a way to display an underline in a label without having the next character interpreted as a mnemonic.

For what it's worth, the operation of mnemonics seems to have changed over the years. Current software, written in recent versions of Swing or JavaFX don't show the underline for the mnemonic until the user presses the `Alt` key. I have earlier versions of programs that always show the mnemonic key underlined although the mnemonics no longer work. Various Windows programs themselves are not consistent in terms of mnemonic usage. For example, [Firefox](http://www.mozilla.org/en-US/firefox/new/) always shows the mnemonic keys underlined while [NetBeans](https://netbeans.org/) does not.

# Shortcuts #

A good argument could be made that shortcuts are not very useful for a program with a button-based interface since buttons do not display the shortcut keys as do menus. The user would actually have to read some program documentation (egad!) in order to discover what shortcuts were available. However, many users are so used to some of the short cuts (`Ctrl-S`, `Ctrl-O`, `Ctrl-C`, `Ctrl-V`, etc.) that I include them anyway.

Setting up shortcuts in JavaFX is a bit more complicated than it was in Swing since you can't seem to do it at the same time you create the button. It seems that the button must be attached to a `Scene` before you have a way to add a shortcut. Maybe this is just due to my own ignorance.

Here's an example of how I set up a shortcut and mnemonic on an "Exit" button in a recent program. I hope the elided parts are obvious.

```clojure
    ...
    (:import
     [ javafx.scene.control Button CheckBox RadioButton ToggleGroup Tooltip]
     [ javafx.scene.input KeyCode KeyCodeCombination KeyCombination
       KeyCombination$Modifier KeyCombination$ModifierValue]
    ...
    
    (defn init-exit-btn-accelerator [btn]
      (.put (.getAccelerators (.getScene btn))
            (KeyCodeCombination.
             KeyCode/X (into-array KeyCombination$Modifier [KeyCombination/SHORTCUT_DOWN]))
             (proxy [Runnable] []
               (run []
                 (.fire btn)))))
    
    (defn create-exit-button-click-handler
      "Handle a click on the 'Exit' button."
      []
      (reify EventHandler
        (handle [this event]
          (Platform/exit))))
    
    (defn create-exit-btn []
      (let [btn (btns/create-styled-button
                 "E_xit" (create-exit-button-click-handler))]
        (.setTooltip btn (Tooltip. "Exit the program."))
        (.setMnemonicParsing btn true)
        btn))
    ...
```

The button is created in the `create-exit-btn` function along with its event handler. The handling for the mnemonic is set up at the same time the button is created.

In the elided code that follows, the button is added to a layout pane, which is eventually attached to a `Scene`. Some time after that, the `init-exit-btn-accelerator` function is called to set up the shortcut. Note that the shortcut and an action handler are set up and added in the same function. As should also be apparent, it is not necessary that using the shortcut must activate the same event handler as a mouse click would. It does in this case, but that doesn't seem to be a requirement.

The shortcut itself is set up by creating a `KeyCodeCombination` with the desired key. This is a bit more complicated than it was in Swing where a text string describing the key could be used as an argument. Not sure what advantage the new method provides.

So every button that gets a shortcut gets this treatment. I haven't done it yet, but it seems like a good candidate for writing a macro to handle creation of the functions from a few pieces of data. Something to do and write about in the future.