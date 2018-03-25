---
title: The Clojure Development Toolchain
tags:
  - clojure
  - editors
  - emacs
  - leiningen
id: 615
comment: false
categories:
  - programming
date: 2013-05-21 21:58:27
modified: 2018-03-16T16:33:45.085662-04:00
---

One of the things about [Clojure](http://clojure.org/) that is difficult for beginners is the process of creating and running programs. I would argue that it is more difficult than learning the language itself. There is no "one-button" provisioning system that would set up some sort of canonical development environment. This long post will talk about setting up Leiningen and Emacs to make a comfortable environment for developing in Clojure.

<!--more-->

**Update 30 Mar 2014**: nREPL has been superceded by [CIDER](https://github.com/clojure-emacs/cider). These instructions now include modifications to install CIDER.

Of course, some readers would argue that you can do Clojure development with NotePad and a command line. While that is true, it is also tedious to the extreme, just as it would be with most other languages. That is what IDEs were developed for -- to take some of the tedium out of development of more complex projects.

In fact, there are some plugins for the [Eclipse](http://www.eclipse.org/) and [IntelliJ IDEA](http://www.jetbrains.com/idea/) IDEs, they don't seem to get the job done well. There was also the [enclojure](http://enclojure.wikispaces.com/)(Broken Link) plugin for my favorite IDE, [NetBeans](https://netbeans.org/), although it is no longer maintained.

At a minimum, a developer needs a good editor and a good build/dependency tool. A good editor "knows" the language. For Lisp-based languages, there is nothing better than Emacs, despite the steep learning curve. (Good arguments have been made for the [Vim](http://www.vim.org/) editor as well.)

The one system that I have found that gets closest to the "one button" install is [Lisp Cabinet](http://lispcabinet.sourceforge.net/). I've written about it before [here](http://yo-dave.com/2011/02/25/2011-02-25-getting-started-with-lispschemeclojure/). It is a terrific piece of work that has served me well.

That said, it has not been without problems for me. I have been using version 0.34 and earlier for some time. The problem was that I could not get [Swank](https://github.com/technomancy/swank-clojure) to work when a version of Clojure later than 1.4 was used in a Leiningen project file. Don't know why. Couldn't figure it out. Gave up trying. (Swank is used to create a Clojure [REPL](http://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) within Emacs that can be used for interactive development. It makes available a number of helpful commands through a facility called [SLIME](http://common-lisp.net/project/slime/), the Superior Lisp Interaction Mode for Emacs.)

A new version of Lisp Cabinet, version 0.35, came out recently. It was built to use Clojure 1.5.1\. Great! Except once again Swank would not work. Could not quickly figure out why (again.) Did not want to spend time investigating. The thing that pushed me over the edge is that Swank has always seemed a bit fragile and is now deprecated by the author. <del>Instead Clojure development is moving to use [nREPL](https://github.com/clojure/tools.nrepl), a networked Clojure REPL.</del> **Update 30 Mar 2014**: Instead Clojure development is moving to use [CIDER](https://github.com/clojure-emacs/cider), a networked Clojure REPL.

So, I've decided to just bite the bullet and put together my own system, installing raw Emacs and configuring it to my tastes along with Leiningen. This post is intended to be a record of my notes so I can do it again some time and provide guidance to other interested readers. Maybe someday I'll script it all together and create the "one-button" installation that I keep whining about.

## My Configuration ##

*   My development system is Windows 7, 64 bit.
*   As this is written, I am running JDK 1.7.0 Update 21, 64 bit.
*   I am running Windows Powershell 1.0\. You can download it free [here](http://www.microsoft.com/en-us/download/details.aspx?id=7217).

## The Build System, Leiningen ##

It may be odd to start with the build system, but it's the shorter and simpler of the two tools to set up.

*   On Windows, download the batch file [here](https://github.com/technomancy/leiningen). (The link to the download is in the "Installation" section of the page. Don't use the version of the file in the "bin" directory.)
*   I put the batch file in `C:\tools\lein` on my system and put that directory on the path.
*   Then run `lein self-install` to do the installation.
*   Open a command window and enter <span style="font-family: Courier New;">`lein version`</span> to assure that the installation worked and that lein runs correctly. On my system, the program replied `Leiningen 2.1.3 on Java 1.7.0_21 Java HotSpot(TM) 64-Bit Server VM`.
*   If you check your home directory, you should see a new directory, `.lein`. Within that directory there will be an additional directory, `self-installs`, which contains the lein jar file. In my case, that file is named `leiningen-2.1.3-standalone.jar`. That's it. You're done.
*   **Update 30 Mar 2014**: Added the `LEIN_JAVA_CMD` environment variable as described in the sample Leiningen project [here](https://github.com/technomancy/leiningen/blob/master/sample.project.clj). This lets you specify which JVM Leiningen uses since it does not seem to use the latest version on Windows without setting this variable.

## The Editor, Emacs ##

I picked up a pre-compiled binary of Emacs 24.3 for Windows (emacs-24.3-bin-i386.zip) [here](http://ftp.gnu.org/gnu/emacs/windows/). I unzipped the file into my `C:\tools` directory. (I use this directory rather than the more traditional `Program Files` directory because, even after all these years, many programs cannot handle directory names with spaces in them.) This creates and fills a directory named `emacs-24.3`. I renamed mine to be simply `emacs`. Within `C:\tools\emacs\bin` find the `addpm.exe` file and execute it. This will create a desktop shortcut. Start up Emacs and review the tutorial material, if needed. There are tons of helpful information on using Emacs right within the program itself.**
**

### Configuration ###

The plain vanilla version of Emacs that you have already started is fine by itself but can be customized to any extent you want. There are several types of customizations we will cover on our way to a pleasant setup for Clojure development.

#### Self-Contained Customizations ####

I call one set of customization "Self-Contained" since they can be accessed from the program itself and require no additional work.

*   First, I don't care for the toolbar. From the menu, select **Options>Show/Hide>Toolbar** to turn it off.
*   Second, I like the Ctrl-C/Ctrl-X/Ctrl-V keys for copy, cut and paste. From the menu, select **Options>Use CUA Keys (Cut/Paste with C-x/C-c/C-v)**.
*   Third, I find editing lisp is easier when matching parentheses are highlighted as you pass over the other parenthesis in a pair. Select **Options>Highlight Matching Parentheses** to enable this operation.
*   Fourth, I prefer to use Consolas as the typeface when editing programs. Again, from the **Options** menu select the **Set Default Font...** dialog. Pick the typeface you prefer.
*   Fifth, I don't really care for the default color scheme, especially when writing Clojure code. Emacs has many ways to change the color scheme. Fortunately, as of version 24, it has some built-in support for changing themes. From the menu, choose **Options>Customize Emacs>Custom Themes**. In the dialog that appears, choose a theme you like and click the **Save Theme Settings** button. Finally, select **Options>Save Options** and quit Emacs (C-x C-c). If you check your home directory, you should see two changes. There should be a new directory named `.emacs.d` and a new file named `.emacs`. Much of the customization information for Emacs is stored in the `.emacs` file. Here's what mine looked like at this stage in the customization:

```lisp
 ;; custom-set-variables was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 '(cua-mode t nil (cua-base))
 '(custom-enabled-themes (quote (wombat)))
 '(show-paren-mode t)
 '(tool-bar-mode nil))
(custom-set-faces
 ;; custom-set-faces was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 '(default ((t (:family "Consolas" :foundry "outline" :slant normal :weight normal :height 120 :width normal)))))
```

#### Adding Customizations with Packages ####

Like many other pieces of software, the operation of Emacs can be modified by loading external packages. To get access to the packages we are interested in, we need to specify a different repository in addition to the one Emacs uses by default. We do that by editing the contents of the `.emacs` file. (`C-x C-f ~\.emacs`) To let Emacs gain access to the [Melpa](http://melpa.milkbox.net/) repository, add the following to your `.emacs` file between the two customization sections that are already present. (I put this between the two sections since any changes to the built-in customizations re-write them to the beginning and end of the file.)

```lisp
;; Customize emacs package handling with the melpa repository
(package-initialize)
(add-to-list 'package-archives
             '("melpa" . "http://melpa.milkbox.net/packages/") t)
```

Place the cursor at the end of each of the two expressions and press `C-x C-e` to evaluate the expressions. Now, if you execute the command `M-x package-list-packages` you will see a long list of additional packages that are available. In that list, put your cursor in the line listing the following packages and press "i" (for install) on each of the package names. For our purposes, select the following list of packages:

*   **Update 30 Mar 2014**: <del>ac-nrepl</del> Do not install this. We will be using CIDER instead. If it is already installed, remove it.
*   auto-complete
*   clojure-mode
*   popup
*   rainbow-delimiters
(There is an additional package you might want to check out some time, paredit. Some folks swear by it. In my experience, it causes nothing but trouble. I've lost count of the number of failed compiles due to paredit thinking it knows what I wanted better than I know myself.)

Confirm that you want the selected packages by typing "x" and installation will start.

**Clojure Mode**. Clojure mode should "just work". To confirm this, open or create a file of Clojure code. When you do, you should see Clojure appear in the Emacs menu bar. Explore the menu for useful shortcuts you should memorize.

Even though the packages are installed at this point, most are not configured to do anything yet. They each need a little push from some additional code in the `.emacs` file. So, open the file and let's do some editing.

**Rainbow-Delimiters.** Every Lisp programmer needs some way to keep track of how parentheses and similar characters (like brackets, braces, _etc_.) match up. That's what the rainbow-delimiters package is for. It colors matching delimiters at the same level in the same color. Enter the following in your `.emacs` file.

```lisp
;; rainbow delimiters parentheses matching
(global-rainbow-delimiters-mode)
```

Type `C-x C-e` and you should immediately see a change in your `.emacs` file. Parentheses at different levels should be colored differently.

**Update 30 Mar 2014**

<del>**nREPL**. The nREPL package adds the ability to run a Clojure REPL within Emacs. Add the following to your `.emacs` file.</del>
```lisp
;;; Enable nrepl and bind it to a key
(add-hook 'nrepl-interaction-mode-hook 'nrepl-turn-on-eldoc-mode)
(setq nrepl-popup-stacktraces nil)
(add-to-list 'same-window-buffer-names "*nrepl*")
;(global-set-key [f2] 'nrepl-jack-in
```

**CIDER.** The CIDER package adds the ability to run a Clojure REPL within Emacs. Add the following to your `.emacs` file.

```lisp
;; Enable cider and bind it to a key
(add-hook 'cider-mode-hook 'cider-turn-on-eldoc-mode)
(setq nrepl-hide-special-buffers t)
(setq cider-popup-stacktraces nil)
(add-to-list 'same-window-buffer-names "*cider-repl*")
(global-set-key [f2] 'cider-jack-in)
```

Use the F2 key as a shortcut to start up the REPL. Note that you can start multiple REPLs in the same session.

**Popup and auto-complete<del>, and ac-nrepl</del>**. The `popup` package provides a nice API to pop up windows over an Emacs buffer while editing. The `auto-complete` package lets Emacs pop up possible completions as you edit Clojure code.<del> The `ac-nrepl` package provides similar functionality when working in the REPL.</del>

```lisp
;; Auto complete
(require 'auto-complete-config)
(ac-config-default)
(define-key ac-completing-map "\M-/" 'ac-stop) ; use M-/ to stop completion

<del>;; ac-nrepl</del>
<del>(require 'ac-nrepl)</del>
<del>(add-hook 'nrepl-mode-hook 'ac-nrepl-setup)</del>
<del>(add-hook 'nrepl-interaction-mode-hook 'ac-nrepl-setup)</del>
<del>(eval-after-load "auto-complete" '(add-to-list 'ac-modes 'nrepl-mode))</del>
```

Whether editing source or working in the REPL, when the window of choices pops up, you use `M-n` to move to the next choice, `M-p` to move to the previous choice. Sometimes auto-completion can be a bit annoying. You can turn it off with `M-/`.

Your Own Customizations

In addition to all the built-in modifications and those you can make by adding packages, there is nothing to stop you from creating your own changes to the `.emacs` file. One thing I like when editing Clojure or any other Lisp is automatic indentation. You can get Emacs to do the indentation for you by adding the following lines to `.emacs`.

```lisp
;; automatic indentation for clojure
(add-hook 'clojure-mode-hook '(lambda ()
(local-set-key (kbd "RET") 'newline-and-indent)))
```

As has often been commented, it seems like Emacs is infinitely configurable. There are lots of areas to changes that might make you work easier. I encourage you to explore additional configurations options. One thing you might consider before doing too much experimentation is backing up your `.emacs` file in an online code repository or paste heap. That way if you screw it up somehow, it will always be possible to go back to a working version.

With these changes, you now have a pleasant development environment for that most pleasant of development languages, Clojure.
