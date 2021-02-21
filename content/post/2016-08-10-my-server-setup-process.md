---
title: My Server Setup Process
tags:
  - pcs (and macs)
  - linux
  - servers
  - ubuntu
date: 2016-08-10 15:58:09
modified: 2018-03-17T10:40:37.259055-04:00
---

[Earlier](https://yo-dave.com/2016/02/12/cloudatcost-setup-notes/), I posted about how I set up servers hosted at [CloudAtCost](http://cloudatcost.com/). There have been a few changes to that process. Rather than continually patching that post, here&#8217;s a new description. Again, this is for [Ubuntu](http://www.ubuntu.com/) 14.04 which gets upgraded to 16.04 as part of the setup.

<!--more-->

So, starting from a freshly imaged Ubuntu 14.04 system:

## Update the System

The process starts by logging into the SSH server newly imaged machine as root. The first thing to do is update Ubuntu with the following commands

```shell
root@ubuntu:~# apt-get update
root@ubuntu:~# apt-get upgrade
```

This can take some time as it will upgrade a _lot_ of files.

After the upgrade is complete, you may be prompted to reboot. Hold off a second.

Now is as good a time as any to reset the host name. This involves simple edits to two files. In no particular order:

```shell
root@ubuntu:~# nano /etc/hostname
```

This file contains a single line, which you should set to your host name, e.g. `example.com`.

Next, edit the `hosts` file (`nano /etc/hosts`). Find the line that contains the IP address 127.0.**1**.1\. Change it to include your new host name, e.g.

```shell
127.0.1.1    example.com
```

Now, reboot.

```shell
root@ubuntu:~# reboot
```

This will break the SSH connection of course. So wait a few minutes for the reboot to complete, then log back in.

When you log back in, your shell prompt should be different, showing the change to the host name:

```shell
root@example:~#
```

There may also be a message about the availability of a new Ubuntu release.

```shell
New release '16.04.1 LTS' available.
Run 'do-release-upgrade' to upgrade to it.
```

If your hosting service allows it, just do it. Again, it can be a bit of a long wait. After the system checks on what it needs to remove and install to do the update, it will ask you if you want to proceed. It allows you to cancel or check on details before proceeding. If you want the upgrade, respond with a "y" to start the upgrade. On my systems, there are a few prompts about updating scripts (or not). Just select the defaults, which don't make changes.

At the end of the process, go ahead and reboot again.

When you log back in, you will be running Ubuntu 16.04\. If there is no log on message showing the version, you can check with

```shell
root@example:~# lsb_release -a
```

On my system, that displays the following message.

```shell
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 16.04.1 LTS
Release:        16.04
Codename:       xenial
```

## Create a New User with Super Powers

Next, we want to add a new user who is not root but can have all of the powers of root when needed.

```shell
root@example:~# adduser user-name
Adding user 'user-name' ...
Adding new group 'user-name' (1001) ...
Adding new user 'user-name' (1001) with group 'user-name' ...
Creating home directory '/home/user-name' ...
Copying files from '/etc/skel' ...
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
root@example:~# 
```

Of course, the name you use can be anything, not just "user-name" (or "root").

At this point, we can tell the system to allow the new user root access by adding them to the "sudo" group.

```shell
root@example:~# gpasswd -a user-name sudo
Adding user user-name to group sudo
```

Now, you can log off as root and log back in as your new user. It's important to know that you can log in with this new user because in a moment, we are going to change settings such that you will no longer be able to log in remotely as root.

## Set Up Public Key SSH Access

Public key access is an additional method to authenticate yourself when signing into your server. It is considered to be more secure and flexible, but a little more difficult to set up. I think it is only more difficult in that there are more steps to do, none of which are particularly difficult. Just sayin'.

First off, you need public and private encryption keys. If you don't know what I'm talking about, [take a look here](http://tartarus.org/~simon/putty-snapshots/htmldoc/Chapter8.html). After reading it, if you really think it' too difficult, you can skip the remainder of this section, otherwise, follow along.

I usually start by changing the permissions of my home directory.

```shell
user-name@example:~$ chmod 750 ~
```

These permissions mean I can read/write/execute in my directory. Members of my group (usually no one else at this point) can read and execute, but not write into the directory. Others can't do any of those things.

Next you need to create a place to save public encryption keys. By convention, this is a sub-directory of your home directory.

```shell
user-name@example:~$ mkdir .ssh
user-name@example:~$ chmod 700 .ssh
```

These permissions mean that only I can read/write/execute in the `.ssh` directory.

Since I do it so often, I have a file on the system that I use to set up the servers called `authorized_keys`. In that file, I have concatenated all of the public encryption keys that I tend to use on my servers. It's just a text file that I need to be able to send to the new server. **Make sure** that only your _public_ keys go in this file.

Now you need to be able to transfer that file into the `.ssh` directory on the server. If you only have PuTTY to work with, it's easy. Open the file in Windows first using whatever text editor you prefer and copy all of it to the clipboard (`Ctrl-A`, `Ctrl-C`). Then, on the server create the file, open it for editing, paste the contents of the clipboard (`Shift-Ins` is the PuTTY keystroke to paste from the clipboard), and finally save and close the file.

```shell
user-name@example:~$ touch .ssh/authorized_keys
user-name@example:~$ nano .ssh/authorized_keys
```

Next, set the permissions on the new `authorized_keys` file.

```shell
user-name@example:~$ chmod 600 .ssh/authorized_keys
```

These permissions mean that only you can read or write the file.

Now we want to change some of the things about the way the SSH program works. I change my settings such that SSH no longer uses the default port, no longer allows root logins to SSH, and allows only specific users to log in. As a super user, fire up the nano editor on the `/etc/ssh/sshd_config file`.

```shell
user-name@example:~$ sudo nano /etc/ssh/sshd_config
```

First look for the line that says "Port 22" and change it to "Port 1935" or some other number. This line is about the fifth line in the file on my system.

Then find the line that reads "PermitRootLogin yes". Change "yes" to "no".

Next, go all the way to the bottom of the file. Add a few new lines with the following:

```shell
# user-name
AllowUsers user-name
# user-name
```

The lines beginning with the "#" character are just comments to remind us of who made the change. Again, substitute your actual user name for "user-name".

Save the file and close it (Ctrl-X, "y", Return).

Now, restart the SSH service with:

```shell
user-name@example:~$ sudo service ssh restart
ssh stop/waiting
ssh start/running, process 1402
```

The process number you see is likely to be different.

At this point you should still be able to log in with a password. Keep your existing session open. Open a new session and verify that you can log in with your password. In yet another session, verify that you can log in using public key access. This will usually involve starting SSH with a modified command line or starting PuTTY with a slightly different configuration. Either way, you must tell the client on your local machine where to access the **private** key to use with the public key on the server. Since I use PuTTy for this on Windows, I have a saved configuration the access to my private login credentials.

Once you have verified that you can log in using your public keys, edit the `/etc/ssh/sshd_config` file. Find the lines that read:

```shell
# Change to no to disable tunnelled clear text passwords
#PasswordAuthentication yes
```

Uncomment the line and change the "yes" to "no". Restart the ssh service.

```shell
user-name@example:~$ sudo service ssh restart
ssh stop/waiting
ssh start/running, process 1402
```

Now, only authorized users can log in and they must use the more secure public key access.

## Configuring and Enabling the Firewall

Ubuntu ships with ufw (the Uncomplicated Firewall) but does not enable it by default. For our purposes, we need a few changes then we can enable it. First, we need the change the firewall configuration a bit to let SSH listen on the port you specified in the `sshd_config` file. Then, if you are running a web server, you will want to allow HTTP access on port 80\. If you are running HTTPS on a server, you will want to pass port 443 through as well.

```shell
user-name@example:~$ sudo ufw status
Status: inactive
user-name@example:~$ sudo ufw allow your_port_number/tcp
Rules updated
Rules updated (v6)
user-name@example:~$ sudo ufw allow 80/tcp
Rules updated
Rules updated (v6)
user-name@example:~$ sudo ufw allow 443/tcp
Rules updated
Rules updated (v6)
user-name@example:~$ sudo ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
user-name@example:~$
```

## Quarantine Bad Actors with Fail2Ban

[Fail2ban](http://www.fail2ban.org/wiki/index.php/Main_Page) is daemon that monitors frequent attempts to log into your system, and probing for exploits. It will ban IPs that repeatedly exhibit these suspicious activities. It's easy to set up and is pre-configured to work pretty well. It's "set it and forget it."

```shell
user-name@example:~$ sudo apt-get install fail2ban
```

## Automate Security Updates

The `sudo apt-get update; sudo apt-get upgrade` commands are almost reflexive for me now. However, in the event that you forget to do it for awhile or (heaven forbid!) go on an unplugged vacation, you can safely automate some of the update process. Generally, it's OK to automate security updates because of their few dependencies. Two files need to be edited/checked to enable automatic updating of security patches.

First, set the timing of updates by editing `/etc/apt/apt.conf.d/10periodic`. Modify it to look like this:

```shell
APT::Periodic::Update-Package-Lists "1"; 
APT::Periodic::Download-Upgradeable-Packages "1"; 
APT::Periodic::AutocleanInterval "7"; 
APT::Periodic::Unattended-Upgrade "1";
```

The contents are pretty self-explanatory.

Then edit `/etc/apt/apt.conf.d/50unattended-upgrades`. It should already be set up just right. Almost everything in the file is commented out. Just make sure that the first section looks like this:

```shell
// Automatically upgrade packages from these (origin:archive) pairs
Unattended-Upgrade::Allowed-Origins {
        "${distro_id}:${distro_codename}-security";
//      "${distro_id}:${distro_codename}-updates";
//      "${distro_id}:${distro_codename}-proposed";
//      "${distro_id}:${distro_codename}-backports";
};
```

As the name suggests, this allows security upgrades to run unattended but prevents others.

## Time Zones and the Network Time Protocol

It's likely that the time zone is already set correctly, but you can check with:

```shell
user-name@ubuntu:~$ sudo dpkg-reconfigure tzdata
... your local time ...
... universal time ...
```

You can assure synchronization with the Network Time Protocol (NTP) by installing the ntp package.

```shell
user-name@ubuntu:~$ sudo apt-get install ntp
```

At this point, you should be set up and ready to go.
