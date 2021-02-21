---
title: Default JavaFX Platform in NetBeans
tags:
  - java
  - netbeans
id: 661
comment: false
categories:
  - programming
date: 2013-06-19 16:26:49
---

**Update**: With Java 8, the JavaFX library is included in the JDK. The machinations described here are no longer necessary.

Every time I update the default JDK used by NetBeans, I also want to update the default JavaFX platform. And I always forget how. Looking on the web usually results in finding methods that just don't work. Here's how I do it.

1.  In NetBeans (7.3 as of this writing), select the "Tools" menu and the "Java Platforms" drop-down menu.
2.  In the left pane of the dialog, select "Default JavaFX Platform" and remove it. Close the dialog by clicking the "Close" button.
3.  From the "File" menu, select "New Project..."
4.  Create a new "JavaFX Application", then complete the wizard using the defaults supplied.
That will reset the Default JavaFX Platform to point to the version included in the latest JDK.