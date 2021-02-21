---
title: How this Blog Disappeared (and Reappeared) Again
date: 2015-05-06
tags: 
  - blogging 
  - configuration 
  - WordPress
---

Some of you may have noticed that this blog disappeared again for awhile. It was on purpose this time.

As I noted in an earlier post, getting [WordPress](https://wordpress.org/) to work on this host has been a bit of a chore. I got tired of doing updates and installs of plugins and such manually. It was not particularly hard, just tiresome.

And then some warnings about particularly nasty XSS bugs appeared. At that time, I took the blog offline until I had some time to deal with things.

Well that time is now. I’m still using [CloudAtCost](http://cloudatcost.com/) as the host for this site. I took advantage of their offer to upgrade my service to their CloudPRO offering. Since I did that, I thought I would look at trying to re-install WordPress in a more satisfactory configuration.

Enter [easyengine](https://easyengine.io). They advertise a two-step install:

```bash
	wget -qO ee rt.cx/ee && sudo bash ee   # install easy-engine
	ee site create example.com --wp        # install wordpress on example.com
```

Really! That’s all there was to it. The site works fine. Updating and installations are now essentially automatic. Someday I’ll go back and look at the new permissions to see what was messed up before. But not today.

Ah, life is good again.
