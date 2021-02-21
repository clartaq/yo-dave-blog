---
title: River5 on Windows
date: 2016-02-22
modified: 2018-03-17T09:43:06.897127-04:00
tags:
  - platforms
  - servers
  - windows
  - raspberrypi
categories:
  - pcs (and macs)
---

As you know, I'm a big fan of [Dave Winer](https://en.wikipedia.org/wiki/Dave_Winer) and his [River of News](http://scripting.com/2014/06/02/whatIsARiverOfNewsAggregator.html) aggregators. His recent [River5](https://github.com/scripting/river5) is particularly nice.

(**Update**: 17 Mar 2018. Although these notes discuss setting up River5 on a virtual server, I have used these instructions to set it up on a [Raspberry Pi 3 Model B](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/) sitting on my desk. It has been running for months without issue. In fact, it's more reliable than my CloudAtCost rented server ever was.)

I've already [posted](https://clartaq.github.io/yo-dave/2016/02/12/2016-02-12-cloudatcost-setup-notes/) about installing on a [CloudAtCost](http://cloudatcost.com/) server running [Ubuntu](http://www.ubuntu.com/). But it seems some folks are running into difficulty installing on Windows. It's easy and works like a dream. Here's how.

<!--more-->

**Note**: This post describes how I set up River5 on my personal desktop. I don't have a Windows server system set up anywhere. The instructions for such a server might be similar, but I don't know.

## Installing Node.js ##

If you have not done so already, install nodejs. Binary installers can be downloaded [here](https://nodejs.org/en/download/).

After running the installer, you can check for correct installation in a Terminal like this:

```batchfile
C:\Users\david>node -v
v4.3.0
C:\Users\david>npm -version
2.14.3
```

**Note**: I use the terms "Terminal" and "Command Prompt" interchangeably in this post. These both refer to the window displayed by running the `cmd.exe` program. You can launch the program multiple ways including from the Start Menu or the File Explorer or an icon on the desktop. Probably other ways too. Refer to the help on your system if you don't know how to start the program.

## Download, Install, and Start River5 ##

As in Dave's [original instructions](https://github.com/scripting/river5/blob/master/docs/FORPOETS.md), start by downloading the zip file containing River5. Just click this [link](https://github.com/scripting/river5/archive/master.zip) to download the file `river5-master.zip`.

On my system, the file ends up in `C:\Users\david\Downloads\river-5-master.zip`. Your location will vary depending on your user name ("david" in my case) and where you have configured your browser to store things downloaded from the internet.

### Now Unzip it Somewhere ##

Now you need to extract the files contained in the zip'ed file you just downloaded. Unfortunately, there are many different ways to do this. On my system, I've purchased a license for [WinZip](http://www.winzip.com/win/en/index.htm) (you can try it for free if you want.) Use the zip tool of your choice to unzip the file to the location of your choice. River5 is not particularly picky about its location, but you should be. I put mine in `C:\projects`. 

The process of unzipping created a directory `C:\projects\river5-master`. You can change the name of the directory if you want. Dave's instructions suggest `river`. That's probably better since the software on your system will not reflect any changes Dave makes on the master branch of his repository.

**Note**: This is much simpler if you have [`git`](https://git-scm.com/) installed on your system. However, if you use `git`, you probably don't need any help setting things up.

### Run NPM to Install ###

Change to the directory with the unzipped software and run NPM to install River5. This step is needed to download additional Nodejs packages required by River5. (Note how the command prompt changes after the `cd` command is executed.)

```batchfile
C:\Users\david>cd C:\projects\river5-master
C:\projects\river5-master>npm install
```

This will take a few moments. When completed, a list of installed packages will be displayed.

### Start it Flowing ###

At this point, you can start the River5 software by typing:

```batchfile
C:\projects\river5-master>node river5.js
```

You should see a list of feeds and articles go whizzing by on the terminal.

Open a browser and navigate to `localhost:1337`. It will likely tell you that nothing can be displayed just yet. It takes a bit for all those feeds to be sliced and diced into a displayable form. But you can assure yourself that the software is running by going to the very top of the page and pulling down the menu behind "Stuff". In that menu, click the very top item that says "Dashboard...". A new window will open showing various information about what the aggregator is doing, including the second item, "Server up:", which shows how long the server has been running. You should see it update every few seconds.

That's it! You're running a copy of River5 ready for you to customize with your own feeds.

## Other Stuff ##

**Keeping River5 Running**: Depending on how you use your system, you will stop River5 from running. If you

* Press `Ctrl-C` in the Terminal where River5 is running
* Close the Command Prompt you used to start River5
* Log off of Windows
* Turn off your system

you will stop River5. You *can* switch users (without logging off) and River5 will continue to run in the background. It will also be accessible in a browser launched from the user you switched to.

**Resources**: You might be concerned about leaving this program running continually on your desktop. Don't worry. According to Windows Task Manager, Nodejs is usually dormant, using no measurable CPU time. As it processes feeds, the CPU usage sometimes hits 0.2% on my system. It looks like it uses about 50 MB of RAM. It hasn't slowed down anything else that is running on my system to any noticeable degree.