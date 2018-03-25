---
title: 'Windows, rsync and permissions'
tags:
  - pcs (and macs)
  - linux
  - utility
  - windows
date: 2016-12-31 10:10:20
---

Just a short note on mixing Linux utilities with Windows. I wanted to set up a single button deployment of updates to a static web site. I had been using an [rsync](https://en.wikipedia.org/wiki/Rsync) script, but it required me to manually enter authentication credentials every time it was used to send updates to a remote Ubuntu server.

I set up public key authentication, but it would not work with the permissions of the key files on the Windows machine where the updates were coming from. After struggling with this for a bit, I finally used [Cygwin](https://www.cygwin.com/) running on the Windows machine to set the permissions just as I would on a Linux system. After that, things worked fine -- it's a single click script with no intervention required.

Still don't know how to get this done using Windows only. Too lazy to figure it out.