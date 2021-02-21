---
title: WordPress
date: 2014-10-16 08:10:49
tags: 
  - blogging 
  - configuration 
  - wordpress
---

Now, I *love* [WordPress](https://wordpress.org/). It’s easy to use and easy to administer. Except when it isn’t.

I run multiple sites on multiple servers. For the most part, they are trouble free. Except one site. This site. (**Update 16 Aug 2017**: This was referring to my site on [CloudAtCost](http://cloudatcost.com/).)

You see, I can’t seem to load plugins or new themes or updates for some reason.

I run the same OS (Ubuntu 14.04, 64-bit as this is written) and the same version of WordPress (4.0, “Benny” as this is written). As near as I can tell, everything is configured the same on all the servers. But this one won’t load plugins. So something is obviously different. But I haven’t been able to find it after hours of staring at configuration files, reviewing log files. googling the problem, and reading many, many posts on this exact same problem. Just doesn’t work yet.

Getting plugins to load securely is a complicated dance of permissions, file ownership, and the configuration of PHP, WordPress, and the web server (Apache2 in my case.) Things have to be *just right* for it to work. But, clearly they aren’t. And I can’t see why.
