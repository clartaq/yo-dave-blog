---
title: Updating to Java 8 and NetBeans 8 on Ubuntu
date: 2014-03-30 13:42:07
tags:
  - java
  - netbeans
categories:
  - programming
---

Just some notes on how I updated to the recent releases of Java 8 and NetBeans 8 on Ubuntu 13.10 (64 bit) running the Xfce desktop environment (Xubuntu).

<!--more-->

## Download and install the new JDK ##

As described [here](http://www.webupd8.org/2012/09/install-oracle-java-8-in-ubuntu-via-ppa.html) The gist is to install from a PPA, not the Oracle downloads. At a Linux command prompt, run and complete the following commands (output is elided):

```shell
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

When complete, verify correct installation with the command

```shell
java -version
```

On my system, the output was:

```shell
java version "1.8.0"
Java(TM) SE Runtime Environment (build 1.8.0-b132)
Java HotSpot(TM) 64-Bit Server VM (build 25.0-b70, mixed mode)
```

Note that this also updates the java alternatives. On my system `sudo update-java-alternatives -l` produces the following output:

```shell
java-7-oracle 1 /usr/lib/jvm/java-7-oracle
java-8-oracle 2 /usr/lib/jvm/java-8-oracle
```

You can also set the Java environment variables to default to Java 8 versions with `sudo apt-get install oracle-java8-set-default`.

## Download and Install NetBeans ##

Download and install from the Oracle [download page](https://netbeans.org/downloads/), then follow the installation instructions.To create a desktop icon (and include NetBeans in your Applications Menu) add a desktop file as described [here](http://hanynowsky.wordpress.com/2012/04/27/add-an-entry-for-netbeans-ide-in-ubuntu-12-04-unity-launcher/). This works for Xubuntu as well as the Unity desktop. The contents of my desktop file are:

```desktop
Encoding=UTF-8
Name=NetBeans IDE 8.0
Comment=The Smarter Way to Code
Exec=/bin/sh "/usr/local/netbeans-8.0/bin/netbeans"
Icon=/usr/local/netbeans-8.0/nb/netbeans.png
Categories=Application;Development;Java;IDE
Version=1.0
Type=Application
Terminal=0
```
That's it. Seems to work Ok for me.