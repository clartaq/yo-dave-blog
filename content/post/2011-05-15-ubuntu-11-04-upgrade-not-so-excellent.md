---
title: 'Ubuntu 11.04 Upgrade: Not So Excellent'
tags:
  - linux
  - quality
id: 377
comment: false
categories:
  - pcs (and macs)
date: 2011-05-15 17:03:17
---

The [Ubuntu](http://www.ubuntu.com "Link to Ubuntu Linux home page") [Linux](http://www.linux.com "Link to Linux home page") distribution came out with version 11.04 a few days ago. These upgrades very seldom go smoothly on my systems. This was not an exception.

<!--more-->

I've been using Linux for several years now. Most recently, I've been using the Ubuntu distribution. I've used the last four or five major versions and had been looking forward to the new distribution with the usual mixture of high expectations and dread.

The 10.10 version had been running for months with only one irritating issue -- it wouldn't recognize one of the drives on my system. No biggie. It was the Windows backup drive on a dual boot system. It was never used from Linux, but kind of weird that it wasn't recognized.

The online update appeared to go well until the reboot. The new user interface, Unity, was a bit confusing. I didn't spend time trying to learn it and just switched back to the "classic" interface.

But networking didn't work. Geez! Another networking problem. Linux was not assigning an IP address to the `eth0` interface on boot up. Despite my best efforts, a topic for another post, I could not get the interface configured reliably. It works on Windows but not Linux? That's a switch.

I finally bit the bullet, did another backup, formatted the drive and installed from an ISO image. Again, the installation appeared to work fine. During the install, the system managed to find and apply updates from the network. But on the post-installation reboot, the network interface was not configured.

Oh, and this time, the installer said the system did not have adequate resources to run Unity. Fine with me, but I know it does. It ran after the online upgrade and this is a system with a fast i7 processor, 16GB RAM, 3TB disk storage, and a recent, top-of-the line video card.

As I write this, I'm downloading the version 10.04 ISO image. We'll see if that does the job. Then, all I will have lost is time and a bunch of customizations.

I really hate computers some times.