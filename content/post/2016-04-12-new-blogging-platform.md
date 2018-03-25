---
title: A New Blogging Platform
date: 2016-04-12
modified: 2018-03-12T16:35:58.877186-04:00
tags:
  - cryogen
  - blogging
---

The "Lou the Luddite" blog was originally created as a self-hosted [WordPress](https://wordpress.org/) blog. But this blog was always a bit problematic for some reason. At first, I couldn't get automatic WordPress updates  to work. Then I found [EasyEngine](https://easyengine.io/), which did the install for me and got things running. But it's just a big black box of a script. I don't know all of the details of what it does. That isn't something I'm really comfortable with. So I started looking for some alternatives.

## Static Sites

Static sites seemed like a good idea. Just write stuff and send it off to the site. It's been done before. For example, [GitHub Pages](https://pages.github.com/) are a very popular way to host static sites.

Then I saw [this post](http://jlongster.com/RIP-Over-Engineered-Blog) mentioning the [Cryogen](http://cryogenweb.org/) system. Very nice. Very easy. One of the reasons I like it is because it is written in [Clojure](https://clojure.org/), one of my favorite languages. Just create a new project with [Leiningen](http://leiningen.org/), do some configuration and start writing. It creates a site that you can preview locally, then send the changes off to a server.

Installation is pretty simple. You can deploy a Cryogen site to GitHub Pages if you want. I wanted to put it on one of my self-hosted servers. Basically, you can  copy the Cryogen-created site onto your server, install [nginx](http://nginx.org/en/) on your server, then do a little configuration to get nginx to serve up the contents of your site. When you create new posts, Cryogen rebuilds the site and, after previewing locally, you can send the changes off to your server.

So, I've made the switch. I've moved my (few) existing posts over to [Markdown](https://daringfireball.net/projects/markdown/), built the site, and pointed my domain at it. (I'll keep the old server up for awhile.) I'm pretty satisfied.

Cryogen isn't perfect. I'll probably fiddle the CSS a bit and make some changes to the way copyrights and tags are displayed. But it seems to work for this site. So far.

(**Update 2017-08-23**: This site is no longer created with WordPress or Cryogen. It is written in Markdown and the site is generated with [Hexo](https://hexo.io/).)

(**Update 2018-03-12**: This site is no longer created with WordPress or Cryogen or Hexo. It is written in Markdown and the site is generated with [Hugo](https://gohugo.io).)
