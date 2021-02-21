---
title: Paths with Spaces, I Give Up
date: 2013-08-08
modified: 2018-03-12T15:58:41.334237-04:00
tags:
  - clojure
  - java
  - leiningen
categories:
  - programming
  - rant
---

I've wanted to look into the [Pedestal](http://pedestal.io/) framework for creating web-based applications in [Clojure](http://clojure.org/). However, one of the requirements is [Leiningen](https://github.com/technomancy/leiningen) 2.2.0 or greater. And, as I've written [before](https://yo-dave.com/2013/05/21/the-clojure-development-toolchain/), version 2.2 will not install on my system because of spaces in the path of the user home directory. ("`C:\Users\David Clark`" on my system.)

My user profile name is "david". That's what I use to sign on with. The fact that my home directory uses "David Clark" is an unfortunate result of how the computer was set up at the factory when I custom ordered it. This has been a periodic pain in the ass ever since I got the system because folks who develop tools primarily on other operating systems, just can't seem to deal with spaces in file paths. Even after all these years. It's a kind of snobbery that I just find annoying and trite. Windows exists. Get over it.

Rather than try to fix every program that screws this up, I decided to change my user account and profile to just use "`C:\Users\david`" as my home directory. Not so easy.

To start with, I followed the directions [here](http://www.sevenforums.com/tutorials/147545-user-profile-folder-change-user-account-folder-name.html). That got me most of the way there. Then I checked all of my environment variables and made changes there as needed. Leiningen installation still failed. In a couple places, it was still trying to put stuff in the "`C:\Users\David Clark`" directory or sub-directories.

Most of these tools use the Java system property variable `user.home` to determine what the home directory is. I wrote the following simple program to check what Java was reporting.

```java
/*
 * Program to display a few user-related properties in the
 * Java environment.
 */
package userproperties;

/**
 *
 * @author David Clark
 */
public class UserProperties {

    /**
     * @param args the command line arguments. Not used.
     */
    public static void main(String[] args) {
        String userName = System.getProperty("user.name");
        System.out.println("User Name: " + userName);
        String userHome = System.getProperty("user.home");
        System.out.println("User Home: " + userHome);
        String userDir = System.getProperty("user.dir");
        System.out.println("User Dir: " + userDir);
    }
}
```

Unfortunately, the program reported that my home directory was still "`C:\Users\David Clark`". Also, in a Windows terminal, the command `echo %USERNAME%` still shows the user name as "David Clark", not "david".

After a little more research, I came across [this page](http://stackoverflow.com/questions/2134338/java-user-home-is-being-set-to-userprofile-and-not-being-resolved) that showed how to change some registry variables for the Windows Explorer Shell. Making those changes seems to have taken care of most things. At least Leiningen installs and runs again.

Fixing the `%USERNAME%` environment variable required a little more work. Running `lusrmgr.msc` and changing the appropriate `Name` field under the `Users` group followed by a reboot seems to take care of that.

So, I think things are now the way I want them. Everything seems to work as expected, or I've built in a big problem that will bite me in the ass at some inopportune time in the future ;-)

One other thing of note is that the registry itself contains a warning not to use those keys but either `SHGetFolderPath` or `SHGetKnownFolderPath` function instead. Maybe Java will catch up some day.