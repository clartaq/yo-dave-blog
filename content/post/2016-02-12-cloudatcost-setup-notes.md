---
title: CloudAtCost Setup Notes
date: 2016-02-12
tags:
  - linux
  - servers
categories:
  - programming
---

## Update: 10 Aug 2016 ##
There's a new and improved version of this post [HERE](https://clartaq.github.io/yo-dave/2016/08/10/2016-08-10-my-server-setup-process/). Look at that instead.


----------
 

I've been experimenting with servers from [CloudAtCost](http://cloudatcost.com/). As such, I tear them down and re-image them fairly frequently. These are my notes on how I do initial setup of servers that have been freshly imaged with [Ubuntu](http://www.ubuntu.com/) 14.04 LTS.

<!--more-->

I generally start out making a copy of the server information from the CloudAtCost [panel](https://panel.cloudatcost.com). This includes things like the IP address and the root password, which you will need shortly.

I do the configuration over [SSH](https://en.wikipedia.org/wiki/Secure_Shell). Since I am running on Windows, there are a few options for SSH clients. I like [PuTTY](http://www.putty.org/), but the [Bitvise](https://www.bitvise.com/ssh-client-download) client is nice too.

## Update the System ##

The process starts by logging into the SSH server newly imaged machine as root. The first thing to do is update Ubuntu with the following commands

```bash
root@ubuntu:~# apt-get update
root@ubuntu:~# apt-get upgrade
```

This can take some time as it will upgrade a *lot* of files.

After the upgrade is complete, reboot the system.

```bash
root@ubuntu:~# reboot
```

This will break the SSH connection of course. So wait a few minutes for the reboot to complete, then log back in.

## Create a New User with Super Powers ##

Next, we want to add a new user who is not root but can have all of the powers of root when needed.

```bash
root@ubuntu:~# adduser user-name
Adding user `user-name' ...
Adding new group `user-name' (1001) ...
Adding new user `user-name' (1001) with group `user-name' ...
Creating home directory `/home/user-name' ...
Copying files from `/etc/skel' ...
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
Changing the user information for user-name
Enter the new value, or press ENTER for the default
        Full Name []: Super User
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Is the information correct? [Y/n]
root@ubuntu:~# 
```

Of course, the name you use can be anything, not just "user-name" (or "root").

At this point, we can tell the system to allow the new user root access by adding them to the "sudo" group.

```bash
root@ubuntu:~# gpasswd -a david sudo
Adding user david to group sudo
```

Now, you can log off as root and log back in as your new user. It's important to know that you can log in with this new user because in a moment, we are going to change settings such that you will no longer be able to log in remotely as root.

## Set Up Public Key SSH Access ##

Public key access is an additional method to authenticate yourself when signing into your server. It is considered to be more secure and flexible, but more difficult to set up. I think it is only more difficult in that there are more steps to do, none of which are particularly difficult. Just sayin'.

First off, you need public and private encryption keys. If you don't know what I'm talking about, [take a look here](http://tartarus.org/~simon/putty-snapshots/htmldoc/Chapter8.html). After reading it, if you really think it's too difficult, you can skip the remainder of this section, otherwise, follow along.

I usually start by changing the permissions of my home directory.

```bash
user-name@ubuntu:~$ chmod 750 ~
```

These permissions mean I can read/write/execute in my directory. Members of my group (usually no else at this point) can read and execute, but not write into the directory. Others can't do any of those things.

Next you need to create a place to save public encryption keys. By convention, this is a sub-directory of your home directory.

```bash
user-name@ubuntu:~$ mkdir .ssh
user-name@ubuntu:~$ chmod 700 .ssh
```

These permissions mean that only I can read/write/execute in the `.ssh` directory.

Since I do it so often, I have a file on the system that I use to set up the servers called "authorized_keys". In that file, I have concatenated all of the public encryption keys that I tend to use on my servers. It's just a text file that I need to be able to send to the new server. **Make sure** that only your *public* keys go in this file.

Now you need to be able to transfer that file into the <code>.ssh</code> directory on the server. If you only have PuTTY to work with, it can be a bit of a chore. This is one area where the Bitvise client has an advantage -- it has an SFTP (Secure File Transfer Protocol) client that provides a side-by-side listing of files on the client and the server. Copying the file is a simple matter of dragging the file from one window to the other.

If you are comfortable typing long command lines in the terminal, you can use plink.exe (one of the programs installed with PuTTY) to complete the transfer in a single line. From the directory where PuTTY is installed, type:

```batchfile
C:\Program Files (x86)\PuTTY>type C:\Users\user-name\.ssh | plink.exe -ssh user-name@host-ip -pw password "cat >> .ssh/authorized_keys"
```

Use the appropriate user name, server IP address and server password for your account. If you have any problems or this looks too intimidating, grab a copy of the Bitvise client and use its' SFTP program.

Next, set the permissions on the newly transferred file.

```bash
user-name@ubuntu:~$ chmod 600 .ssh/authorized_keys
```

These permissions mean that only you can read or write the file.

Now we want to change some of the things about the way the SSH program works. I change my settings such that SSH no longer uses the default port, no longer allows root logins to SSH, and allows only specific users to log in. As a super user, fire up the nano editor on the `/etc/ssh/sshd_config file`.

```bash
user-name@ubuntu:~$ sudo nano /etc/ssh/sshd_config
```

First look for the line that says "Port 22" and change it to "Port 1935" or some other number. This line is about the fifth line in the file on my system.

Then find the line that reads "PermitRootLogin yes". Change "yes" to "no".

Next, go all the way to the bottom of the file. Add an empty line and the following:

```text
# user-name
AllowUsers user-name
# user-name
```

The lines beginning with the "#" character are just comments to remind us of who made the change. Again, substitute your actual user name for "user-name".

Close the file and save it (Ctrl-X, "y", Return).

Now, restart the SSH service with:

```bash
user-name@ubuntu:~$ sudo service ssh restart
ssh stop/waiting
ssh start/running, process 1402
```

The process number you see is likely to be different.

Now you need the change the firewall configuration a bit to let SSH listen on the port you specified in the `sshd_config` file.

```bash
user-name@ubuntu:~$ sudo ufw allow 1935/tcp
Rules updated
Rules updated (v6)
```

## Time Zones and the Network Time Protocol ##

It's likely that the time zone is already set correctly, but you can check with:

```bash
user-name@ubuntu:~$ sudo dpkg-reconfigure tzdata
... your local time ...
... universal time ...
```

You can assure synchronization with the Network Time Protocol (NTP) by installing the ntp package.

```bash
user-name@ubuntu:~$ sudo apt-get install ntp
```

At this point, you should be set up and ready to go.

##Update: 17 Mar 2016##

[Fail2ban](http://www.fail2ban.org/wiki/index.php/Main_Page) is daemon that monitors frequent attempts to log into your system, and probing for exploits. It will ban IPs that repeatedly exhibit these suspicious activities. It's easy to set up and is pre-configured to work pretty well. It's "set it and forget it."

```bash
user-name@ubuntu:~$ sudo apt-get install fail2ban
```