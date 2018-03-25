---
title: Another Program I'd Like to Find or Write
date: 2014-03-04 13:26:26
tags:
  - cloud
  - utility
categories:
  - programming
---

As I've [mentioned before](https://clartaq.github.io/yo-dave/2013/08/03/2013-08-03-cloud-storage-at-copy/), I use several cloud storage services. However, being a cheap bastard, I have only signed up for free accounts up to now. That means I have lots of little bits of storage scattered here and there (although recently [Box](https://www.box.com/) gave me 50GB free and my [Copy](https://www.copy.com/) account has risen to 25GB). But that means having to keep track of where I put certain things. It sure would be nice to have a program that would aggregate all these little pieces into a single thing.

Aggregation into something like the [storage pools](https://en.wikipedia.org/wiki/ZFS#Storage_pools) of the [ZFS](https://en.wikipedia.org/wiki/ZFS) file system would be ideal. Having the ability to configure redundancy and RAID-like capabilities would be icing. Additional requirements would include cross platform operation and strong encryption on the client side.

Of course, using multiple cloud services would introduce more possible sources of failure. What should the system do if one of the services goes down? What should the system do if one of the services decides to shut down? More complications.

Such a program would have to understand the API of each service. That would be another great project -- developing a library that abstracts away the differences between services -- basically a ZFS-like file system for the cloud.

Why have I not written such a thing myself? As I have whined about before, I spent the last 30 - 40 years writing desktop and embedded systems. Not too much work on web-based applications. Although I'm learning, I would rather have such an application before I have the skill to write it. Any takers?