---
title: My Experience with 1999 Blogging Software
date: 2016-06-05
modified: 2018-03-17T10:18:39.542564-04:00
tags:
  - blogging
---

tldr; This long post contains my impressions of setting up and using the [1999.io](http://1999.io/) blogging platform on a remote Ubuntu server.

Blogging is a great thing. It lets anybody say what they want to whatever audience is interested. It's relatively simple for anyone to set up. (Me setting up this blog for example.) But it's not *dead* simple. Particularly if you want to self-host your site rather than putting it on someone else's machine, for example [WordPress.com](https://wordpress.com/).

There is also a fair amount of friction in writing the blog entries themselves. It isn't hard, but it isn't as easy as it could be.

[Dave Winer](https://en.wikipedia.org/wiki/Dave_Winer) has been working on these problems for a long time. He has a new project called [1999.io](http://1999.io/) that makes writing and posting just about as easy as can be. Here are my impressions of setting up and using that software.

So, what is it like to setup and use a 1999 blogging system?

## My Experience and Impressions ##

### Setting Up the Software ###

If you don't have a [twitter](https://twitter.com/) account, get one. You will use your twitter credentials to sign in the 1999 system you set up. I had never used twitter up to this point, but their authentication API seems like a useful thing.

You need a server too. The documentation has a very good description of installing the software on an [Ubuntu](http://www.ubuntu.com/) server.

If you haven't got one, get one and configure it as described [here](http://yo-dave.com/2016/08/10/2016-08-10-my-server-setup-process/). I set up a minimal server as a test system on [CloudAtCost](http://cloudatcost.com/).

After your server is all set up, you can follow the directions in the [setup instructions](https://github.com/scripting/1999-project/blob/master/docs/setup.md) for 1999.io. These instructions seem very complete except perhaps for how you configure your firewall to allow access to the appropriate port and websocket for the application. UFW (Uncomplicated Firewall) is easy to use and configure. The folks at [Digital Ocean](https://www.digitalocean.com/) have a good walk-through of how to use it on Ubuntu [here](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-14-04).

### Using the Software ###

Starting to use the software consists of pointing a browser at the system you set up. The initial page contains a header with a title and some drop-down menus below which is an area to start typing and a button labeled "Post". You type what you want and click the button. That's all there is to it.

Then, later, if you have something to add or want to make a change, you go to the original and just start typing again. The underlying technology assures that those changes get propagated virtually instantly. That is pretty whizzy!

## Pros ##

- Writing, posting, and revising blog posts is just about as easy as possible.
- The project is open source. The *technorati* can do whatever they like with the project to modify it to fit their *exact* needs. It just take some work.
- Ability to write plugins to customize existing behavior or add new capabilities. (See above.)

## Cons ##

There are a few problems, none of them serious or even all that annoying.

- Written in JavaScript, the language everyone loves to hate.
- Uses node.js and that crazy ugly callback programming style.
- Doesn't have a code highlighting plugin yet.
- I wish there were an explanation for setting up a server to use https. With Google's misguided guidance that all sites should use it, it's becoming a bit of a requirement if you are interested in being well represented in search results.
- The ease of changing existing posts is one of the goals of the program, but there does not seem to be a way to go back to previously existing versions. That bothers me a little, but no much.

## Rough Edges ##

I hesitate even to call these "cons" since this is just the way life is now.

- Requires a twitter account. I list this as a rough edge since, until now, I have never used twitter. However, it is easily available to anyone and probably a better choice than another home-brew authentication scheme.

## So, Did Dave Achieve His Goals? ##

I think so. I can't imagine how you could make the process of writing any easier. There is still the one-time hassle of setting up a server. But you only need to to it once and you can get someone else to help you if need be.

After that, it's smooth sailing. Congratulations to Dave Winer!