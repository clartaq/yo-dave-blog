---
title: ssh Cleverness
date: 2015-05-12
tags:
  - linux
categories:
  - pcs (and macs)
---

Just a quick note about using SSH. Recently I had to wipe and re-install Ubuntu. (More on that later.) That meant my ssh private key was wiped as well. In order to replace it, I copied the file from a Windows machine onto my newly installed Linux partition.

When I tried to log into some of my other servers, ssh gave me a warning about lax permissions and refused to log in without my password. Very clever. I never even thought to check the permissions of the file I copied over.

Setting the correct permissions:

```shell
chmod 600 ~/.ssh/id_rsa
```

fixed the problem.
