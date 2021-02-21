---
title: River5 on Ubuntu 14.04 LTS with a CloudAtCost VS
date: 2016-02-14
tags:
 - nodejs
 - servers
categories:
 -- programming
---

[River5](https://github.com/scripting/river5) is the latest in a series of [RSS](https://en.wikipedia.org/wiki/RSS) News Aggretators written by [Dave Winer](https://en.wikipedia.org/wiki/Dave_Winer). You can read his announcement [here](http://scripting.com/liveblog/users/davewiner/2016/02/09/0995.html). These notes describe how to set up a River of News on a Virtual Server (VS) purchased or rented from [CloudAtCost](http://cloudatcost.com/) and freshly imaged with [Ubuntu](http://www.ubuntu.com/) 14.04.

These notes assume that you have done some work in configuring your VS with a user, other than root, that has super user privileges. If you need help doing the initial setup of your VS, look [here](https://yo-dave.com/2016/08/10/my-server-setup-process/).

<!--more-->

VS's don't have graphical displays. Everything needs to be done from the command line. You can execute these commands from the the console provided by the CloudAtCost [panel](https://panel.cloudatcost.com). However, I find the response to be slow. An SSH client tends to work better for me. If you are running from Windows, I find the [PuTTY SSH client](http://www.putty.org/) to be very good.

In the command examples below, the input you would type is preceded by the command prompt. On my system, this is set up to show what directory I am in, followed by a dollar sign ($) and a space. When there is output displayed by a program, it follows a command and is not preceded by the prompt with the dollar sign.

## Installing Node.js ##
The version of [node.js](https://nodejs.org/en/) available from the package manager on Ubuntu is typically woefully behind the current mature, stable release available from nodejs.org. But it's easy to install a current version without the package manager. Execute these commands to download the current version. Note that in my case, the current version was v4.3.0. Vist the Node.js download page to determine the appropriate version number when you install.

```shell
~$ mkdir downloads
~$ cd downloads
~/downloads$ wget https://nodejs.org/dist/v4.3.0/node-v4.3.0-linux-x64.tar.gz
```

This retrieves the package of pre-compiled binaries. If you want to compile the version from source, well, that's a subject for another article.

There is some controversy over where to place the contents of the package in the file system. I've used several locations with success, but the consensus seems to be that Node.js should reside in `/usr/local``. To unpack the package there, execute:

```shell
~$ cd /usr/local
/usr/local$ sudo tar --strip-components 1 -xzf ~/downloads/node-v4.3.0-linux-x64.tar.gz
```

and supply the `sudo` password when prompted. After a few seconds, the command prompt should return with no additional messages displayed.

To assure that Node is in place and ready to run try:

```shell
/usr/local$ cd ~
~$ which node
/usr/local/bin/node
~$ node -v
v4.3.0
```

This shows that Node is indeed installed in the `/usr/local` directory tree and that it has the expected version number.

Likewise, to assure that `npm`, the Node package manager, is present and working run:

```shell
~$ which npm
/usr/local/bin/npm
~$ npm version
{ npm: '2.14.12',
ares: '1.10.1-DEV',
http_parser: '2.5.1',
icu: '56.1',
modules: '46',
node: '4.3.0',
openssl: '1.0.2f',
uv: '1.8.0',
v8: '4.5.103.35',
zlib: '1.2.8' }
```

Remember that these version numbers are those that were produced on my system when I wrote these notes. The values that you see might be different.

## Install River5 ##

Installing River5 is even easier. First install the `git` version control tool.

```shell
~$ sudo apt-get install git
```

Then use `git` to retrieve River5 from its repository.

```shell
~$ git clone https://github.com/scripting/river5
```

To install other packages needed by the program:

```shell
~$ cd river5
~/river5$ npm install
```

This will display a relatively long list of packages needed by the program.

To start it up:

```shell
~/river5$ node river5.js
```

You should see a list of feeds and article titles go scrolling by. After a few minutes point a browser at `your_domain:1337` where `your_domain` is a domain name that points to your server. If you have not obtained a domain name (or the DNS has not updated to the new IP address obtained when you imaged your server), you can also use the IP address provided when you imaged your VS.

## Letting It Flow ##

If you log off of your server now, River5 will also stop operating. Go ahead and try it now. Just close the command shell on the server and try to refresh the web page. It won't work.

In order to keep the river flowing, even when you aren't logged in, you need to do a little additional work.

There are a few ways to keep processes running when you aren't logged in, but my favorite is [Forever](https://github.com/foreverjs/forever). This is a package that is installed with `npm`.

```shell
~$ sudo npm install forever -g
```

On my system, I got a message about an optional dependency failing. (It was for an event handling package for Macs -- not needed on Ubuntu.) You can safely ignore it.

Then use Forever to start River5.

```shell
~$ cd river5
~/river5$ forever start river5.js
```

Again, you may see a few warnings that can be safely ignored. (They relate to some advanced configuration options for Forever, which we don't need.)

That should do it. Within moments, you should be able to refresh you browser and see the web page again. It may take a few more minutes for new content to appear.

## Miscellaneous Stuff ##

I have exercised these notes on different VS's with differing levels of resources. The minimum system I have used is 1 CPU, 512 MB RAM, and 10 GB disk. (That's the smallest system supported by CloudAtCost too.) I have also tried it on servers with 4 CPUs, 3 GB RAM and 40 GB of disk space. Seems to work fine.

When you look at your server on the panel at CloudAtCost, you may be alarmed to see memory usage at 80% or above, even if you are on a VS that is rather well equipped with RAM. Don't panic, you aren't really running out of memory. Log in to your server and run:

```shell
~$ free -m
```

to show how your memory is really being used. For example, on a system with only 512 MB, I received the following output.

```text
             total       used       free     shared    buffers     cached
Mem:           490        405         84          0         26        251
-/+ buffers/cache:        127        362
Swap:          953          0        953
```

As you can see, a large portion of the memory is cached and the system is not even using any swap space. (For additional information on interpreting the output of `free`, see this [link](http://corlewsolutions.com/articles/article-6-understanding-the-free-command-in-ubuntu-and-linux).)

I have only tested this on VS's purchased from CloudAtCost. However, these instructions will probably work on similar systems rented from other providers like [DigitalOcean](https://www.digitalocean.com/) and [linode](https://www.linode.com/).

Version 14.04 LTS 64-bit is the only version of Ubuntu supported by CloudAtCost. However, there don't seem to be any instructions in these notes that would prevent them from being used with another recent LTS release. Untested though.
