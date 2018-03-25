---
title: Why This Blog Disappeared for a Few Days
date: 2016-12-08
tags:
  - technology
  - hosting
---

Those of you who visit this site regularly may have noticed that is has been down for a few days. Here's why.

About a week ago, I attempted to reboot the server after applying a few updates. It wouldn't come back up. As I've [written before](https://clartaq.github.io/yo-dave/2014/10/14/2014-10-14a-is-my-hosting-service-a-scam/), this site is hosted on [CloudAtCost](http://cloudatcost.com/). Sorry to say that it is a regular occurrence for servers not to reboot.

Usually, I can just file a ticket and get help in a day or so. This time,
there was silence for four days. This time, the boot process reported an unrecoverable error on the SSD hosting the VM. But I was worried about the length of time it was taking them to reply. Then finally, an acknowledgment of the tick came. Whew!

However, a few days later, another update came saying essentially "We can't fix your VM. You'll have to re-image and start over. Sorry for the inconvenience." What?! Re-image?! Lose everything on the site?!

But that's just what happened. After re-imaging and re-installing, here we are again. It wasn't too bad for this site. Just re-image and configure the server, then use rsync to reload the blog contents.

I still really like CloudAtCost because I can buy lots of really cheap servers to quickly deploy and tear down. I pay for them once and do it over and over. But, man! That seems like some poor customer service. I guess they aren't so good for servers you want to keep for an extended period.

