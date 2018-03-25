---
title: An Email Server on Ubuntu 14.04
tags:
  - pcs (and macs)
  - email
  - linux
date: 2016-08-26 09:59:54
---

Email is pretty old. It is older than the Internet. Despite the perennial claims that email is "dead", it is still going strong. It's also a bit complicated, which is why most folks set up an account with someone else ([Yahoo!](https://mail.yahoo.com/), [Gmail](https://mail.google.com/), [HotMail](https://www.hotmail.com/), [ProtonMail](https://protonmail.com/), [FastMail](https://www.fastmail.com/), _etc_.) who can take care of it for them.

However, if you want to host your own server, it can be done. If you are running [Ubuntu](http://www.ubuntu.com/) 14.04 (and only that version at this time), [MailInABox](https://mailinabox.email/) can handle most of the setup for you automatically.

It takes care of things like anti-virus, anti-spam, grey-listing, and HTTPS for you. The one area it cannot handle for you is the DNS setup. That's something that is typically done with the domain registrar. And it seems to be different at each one, although there are tutorials appearing on the MailInABox web site that walk through the process at specific registrars. That was really the only difficulty I had.

There may be other problems getting other email services to accept email from your new server. They each have some arcane heuristic rules that block email before it even reaches their spam filters.

So, if you're tired of having all of your email read just to send you more advertising, think about setting up your own server. It isn't that hard anymore.