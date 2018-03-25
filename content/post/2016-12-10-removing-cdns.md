---
title: Removing CDNs
date: 2016-12-10
tags:
  - marketing
  - hosting
---

Everyone wants their web site to load quickly using as few resources as possible. Content Delivery Networks (CDNs) are a very attractive way to achieve this. You can put parts of your web site on someone else's servers. Those servers can contain commonly used resources like code libraries, CSS libraries, fonts, and icons. The hardware at the CDN may be faster than your own and might have multiple copies scattered around the world. Faster hardware and a shorter trip through the network can offer a site real speed improvements.

But, ... at what price? Well, typically "free" CDNs spy on their traffic collecting information about who's looking at what that they can sell to advertisers (or whoever wants to buy the information). Typically, this means more (more!) targeted advertising.

There is surely enough of that already. I don't want all these companies spying on visitors to this site. I've already [removed Disqus](https://clartaq.github.io/yo-dave/2015/05/15/2015-05-15-turning-off-disqus/) as a comment platform for just this reason.

Time to do the same thing with CDNs. That means taking some open source resources and serving them directly from this site. It will mean a little more disc usage on my server and probably less snappy page downloads.

If you notice a big decrease in performance over the next several weeks, let me know. Maybe there will be something else that can be done. Anyway, hopefully there will be fewer people spying on you.

**Update:**

I've removed references to all of the CDNs that [Privacy Badger](https://www.eff.org/privacybadger) flagged as tracking issues. Those resources are now served directly from this site. I was particularly surprised that it did *not* flag the [Google Fonts](https://fonts.google.com/) or [Font Awesome](http://fontawesome.io/) download links, but it did not.
