---
author: david
title: Setting Up Remote Repositories for Mercurial
date: 2019-08-25T12:10:31.160-04:00
modified: 2019-08-25T15:20:36.616-04:00
tags:
  - hg
  - Mercurial
  - remote
  - repositories
---

With the recent [announcement](https://bitbucket.org/blog/sunsetting-mercurial-support-in-bitbucket) that Bitbucket is "sunsetting" [Mercurial](https://www.mercurial-scm.org) support, I've been looking at ways of hosting remote copies of my repositories on my own.

In the short term, I have just cloned my repositories from Bitbucket onto a [Raspberry Pi](https://www.raspberrypi.org) 4 sitting behind my home desktop. I use Pi to run a [River of News](http://scripting.com/2014/06/02/whatIsARiverOfNewsAggregator.html) aggregator for my family. It has plenty of capacity for a task like this.

The steps described below are simple but assume that you have [SSH](https://www.ssh.com/ssh/) access to the remote system.

Starting in my home directories on both machines:

#### On the Remote

Initialize a repository where you want to place your clone.

    mkdir projects/my-project-repository
    cd projects/my-project-repository
    hg init

#### On the Local

    cd projects/my-project-repository

If there is an `hgrc` file in the `.hgrc` subdirectory, add/change the `default` line in the `[paths]` section:

    [paths]
    default = ssh://user@server.com:1234//home/user/projects/my-project-repository

where `1234` is the port number you use for `SSH` access if it is different from the default value of 22.

Then:

    hg push ssh://user@server.com:1234//home/user/projects/my-project-repository

You could actually just do an `hg push` if you updated the `default` path in your `hgrc` file.

#### On the Remote

    hg update tip

Rinse and repeat.

For now, I've moved all of my repositories to the Pi and deleted the private ones from Bitbucket. I'll leave the public ones in place until Bitbucket decides to delete them.