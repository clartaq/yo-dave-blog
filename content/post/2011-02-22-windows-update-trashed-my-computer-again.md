---
title: Windows Update Trashed My Computer -- AGAIN!
tags:
  - drivers
  - windows
id: 180
comment: false
categories:
  - pcs (and macs)
date: 2011-02-22 00:23:13
---

Ever since the purchase of a nice new system running Windows 7 Professional, I have been plagued by periodic system crashes. Crashes that make the system unstable and that finally make it unbootable. And it's all the fault of Windows Update.

<!--more-->

The crashes were so severe, they eventually required the re-installation of Windows from a recovery disk, going through the process of activating Windows again, getting all the updates downloaded and installed, reinstalling applications, copying data from backup (when the backups aren't corrupted - worth another post on its own.) A bit more than the usual frustration of dealing with Windows.

Having just gone through this for the umpteenth time, it looks like it has all been due to an optional update suggested by Windows Update, one entitled "Accusys Inc. - Storage - ACS-6xxxx". A little searching says this is due to Windows incorrectly thinking the NEC USB 3.0 device is really an ACS RAID controller of some type. The chatter seems to indicate that this happens on systems with a Marvell 6G Controller, as mine does. Anyway, not installing the ACS driver seems to keep things much more stable.

By the way, my wife has pointed out that this problem occurs every time I have an extra day off or a holiday. Hmmm....