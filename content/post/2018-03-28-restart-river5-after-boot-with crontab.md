---
title: Restart River5 after Reboot with PM2
date: 2018-03-28T10:06:21.861949-04:00
modified: 2018-04-10T10:08:46.969715-04:00
tags:
  - river5
  - pm2
  - raspberry pi
categories:
  - programming
---

I run a [River5](https://github.com/scripting/river5) "[River of News](http://scripting.com/2014/06/02/whatIsARiverOfNewsAggregator.html)" on a [Raspberry Pi](https://www.raspberrypi.org) wirelessly connected to my internal home network. Sometimes, the power fluctuates, and the Pi reboots. I want to have River5 restart when the system comes back up.

There are lots of ways to restart stuff on Linux. (I am running Raspian GNU/Linux 8.0.) The process manager I use is [PM2](http://pm2.keymetrics.io) -- mostly because it knows Nodejs.
River5 is written for Node.

## Install PM2 ##

I assume you already have [Node.js](https://nodejs.org/en/) and [npm](https://www.npmjs.com) installed. If not, get them now.

Once you have `npm`, you can install `PM2` with the following command line:
```shell
npm install pm2 -g
```

## Experiment with Running River5 ##

Depending on how you installed River5, you may need to do some fiddling to get it running in PM2.

In the simplest case, you could just run it from your home directory:

```shell
node river5.js
```

If you installed it somewhere else, you must change your active directory to the same location where the `river5.js` script is stored. In my case, that means I run the following command.

```shell
cd river5
```

Then I can start the river flowing by:

```shell
node river5.js
```

Once you get it working, shut it down (Ctrl-C) and change back to your home directory. Then, use a command like the following to start the River5 server from your home directory.

```shell
pm2 start /home/david/river5/river5.js --cwd /home/david/river5
```

In this case, my username is `david` and I have River5 installed in the `river5` directory. **_Don't_** use relative paths for this.

Assure that you can repeatedly start the River5 server using your command line. Once you are confident that you command line works, from your home directory, run:

```shell
pm2 save
```

The command above will save a set of information related to how PM2 is running your application. The file is in `~/.pm2/dump.pm2`. Have a look at it if you like.

Now we can finally set up the system to restart River5 whenever the system reboots. From your home directory, execute the following command:

```shell
pm2 startup
```

This command will examine your system and figure out what information it needs to restart your app. Once executed you should see something like:

```shell
[PM2] Init System found: systemd
[PM2] You have to run this command as root. Execute the following command:
sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u david --hp /home/david
```

The important thing is that last line. You should run it exactly as written. You may have to supply your password.

Once the command completes, your system should be set up to restart River5 whenever it reboots. You can test it by entering the command:

```shell
sudo reboot now
```

Then wait a few minutes and see if you can connect to your River5 server in a browser. It should render and be serving new stories for you.

