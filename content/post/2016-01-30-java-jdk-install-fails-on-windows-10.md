---
title: Java JDK Install Fails on Windows 10
date: 2016-01-30
tags:
  - upgrading
---

An update to the [Java](http://www.oracle.com/technetwork/java/index.html) JDK is always a good thing. I look forward to seeing all the new stuff. But my recent attempt to update to 1.8.0_72 only partially succeeded. I attempted to install the new updates, both update 71 and 72, and both failed to finish installing the JRE. The rest of the JDK seemed to install correctly and I have been able to use it successfully, but the local JRE failed to install, returning error code 1603.

The error dialog points you to [this page](https://java.com/en/download/help/error_1603.xml). The page helpfully explains that the installation failed to complete and the cause is under investigation. (I’m being facetious here.) The page also has two suggested workarounds. One is to uninstall all previous versions before installing the new version.

Did that. Guess what. The new version still wouldn’t install and now, neither will the older version. (It was originally installed before the upgrade to Windows 10.) Now I can’t get any version of the JRE installed. I’m worse off than when I started.

Man, I’m tired of this kind of stuff.
