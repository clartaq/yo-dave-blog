---
title: Caddy Server and Let's Encrypt
date: 2016-08-09
tags:
  - blogging
  - hosting
---

The observant among you may have noticed a small change in the blog. It's that little lock symbol next to the URL for the post. That means the blog is being served on [HTTPS](https://en.wikipedia.org/wiki/HTTPS) instead of plain ol' [HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol).

Ever since hearing about it, I've had mixed feelings about the push to HTTPS. Of course, it will provide some benefit in terms of security. But if Google weights its search results based on whether or not a site supports it, access to some early web sites may be lost. If those were static sites to begin with, there never was much of a security issue, but they will likely be lost in search results. Likewise, the scary warnings produced by browsers may just be total nonsense for some sites.

Anyway, this is a bit of a big deal for me. I'm not much of a web maven and setting up HTTPS was always interesting, but a bit of a stretch for me, even with [Let's Encrypt](https://letsencrypt.org/).

That has changed now. The [Caddy server](https://caddyserver.com/) takes care of creating and renewing the SSL certificates that HTTPS is based on. It all happens automatically. Caddy is open source, cross-platform and very buzzword compliant. You should really check out the web site. There is a lot of very good documentation to get you going.

It was pretty fast for me to spin up a new virtual server, migrate the blog, then install and configure Caddy. One of the things I like most was the simplicity of the configuration file. There are lots of options, of course, but they seem well-designed and understandable.

The site hasn't been running long enough for the first set of certificates to expire. When they do, they should be renewed automatically. We'll see how that goes when it happens, but based on my experience so far, I'm pretty optimistic.

So much fun.

