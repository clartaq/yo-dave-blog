---
title: Tidying up the Ubuntu Grub2 Boot Menu
tags:
  - grub2
  - linux
  - windows
id: 243
comment: false
categories:
  - pcs (and macs)
  - programming
date: 2011-03-13 01:27:27
---

My wife and I share a computer that dual boots Ubuntu Linux and Windows. Too, when we upgrade computers, I tend to take the hard drive(s) from the old computer to the new computer. The boot menus created by the grub2 program can be a bit confusing for the uninitiated. As kernel updates are added, the list gets longer. The old Windows drive appears in the list, as well as the recovery partitions that manufacturers often put on Windows drives. In the normal course of things the Windows menu items can get pushed out of view at the bottom of the screen.

"Where's the Windows menu item!?" "Which of all these Windows choices is the right one?"

Not a happy situation.

In the interests of domestic harmony (and to assure that a Windows recovery partition is not booted inadvertently), a little clean up is required.

<!--more-->

## Removing Old Kernels

This is probably the easiest step. In Ubuntu, pull down the System/Administration menu and select the Synaptic Package Manager. Then just find all the old Linux kernels in the list. You can search for "Linux" and in the resulting list, pick the ones you want to get rid of. For example,

[THIS IMAGE HAS BEEN LOST]
[![](http://saturdaynitebathsoap.com/blog/wp-content/uploads/2011/03/Screenshot1-1024x453.png "Screenshot")](http://clarkonium.net/?attachment_id=251)

In this image the Linux 2.6.35-24 and 2.6.35-25 kernels are selected for complete removal leaving the 2.6.35-27 kernel in place. (You don't want to remove all of the kernels of course.)

Since the list of items can be quite long, you could also search for the kernel version to make your selection, e.g. "2.6.35-24" and "2.6.35-25".

## Moving Windows to the Top of the Menu

Before you start mucking about with the boot loader, let me emphasize that I'm talking about grub 2 here, not legacy grub.

To get the Windows partitions to the top of the grub menu list, you can just change a file name. Grub builds the menu by running a series of scripts in the `/etc/grub.d` directory. These script modules are named with numerical prefixes. The one that probes for Linux kernels is named `20_linux` on my system. The script that probes for other operating systems is named `30_os-prober` by default. With this naming convention, the various Linux kernels are identified and added to the menu list before the system probes for Windows. To change the order, you can change the name of one of the scripts. On my system, I've changed `30_os-prober` to `09_os-prober`. After making that change, running

```bash
sudo update-grub
```

from a console will rebuild the list with the bootable Windows partitions listed first.

## Hiding Unwanted Windows Partitions

At this point, the grub menu will have the bootable Windows partitions at the top of the menu. To hide any partitions that you don't want to appear, you need to edit the 09_os-prober script. Here's the relevant section of the edited file from my system.

```bash
for OS in ${OSPROBED} ; do
  DEVICE="echo ${OS} | cut -d ':' -f 1"
  LONGNAME="echo ${OS} | cut -d ':' -f 2 | tr '^' ' '"
  LABEL="echo ${OS} | cut -d ':' -f 3 | tr '^' ' '"
  BOOT="echo ${OS} | cut -d ':' -f 4"

  if [ -z "${LONGNAME}" ] ; then
    LONGNAME="${LABEL}"
  fi

  # Added to remove Windows 7 on HP Drive from boot choices
  if [ "$LONGNAME" = "Windows 7 (loader)" ] && [ "${DEVICE}" = "/dev/sda1" ] ; then
  echo "Skipping ${LONGNAME} on ${DEVICE}" >&2
  continue
  fi
  # End Added

  # Added to remove Windows Vista Recovery on HP Drive from boot choices
  if ["$LONGNAME" = "Windows Vista (loader)" ] ; then
  echo "Skipping ${LONGNAME} on ${DEVICE}" >&2
  continue
  fi
  # End Added

  echo "Found ${LONGNAME} on ${DEVICE}" >&2
  found_other_os=1
```

On my system, the first line shown here is at line 149 in the script file. The two sections I added are surrounded by comments. In the first case, I have two bootable Windows 7 partitions. The one I _don't_ want to appear in the boot menu is on `/dev/sda1`. The code checks if the partition it discovers is the Windows 7 loader and is on the specified device. If so, the rest of the code that would add it to the menu is skipped.

In the second case, the same drive has a Windows Vista recovery partition. Since it is the only Windows Vista bootable partition, I don't have to specify the device -- the script just skips any (and the only) Windows Vista partition.

Another alternative that would have worked for me would have been to check for anything on `/dev/sda` and ignoring them all. This is just a bit easier for me to understand when I come back after a few months of not thinking about it.

After making these changes, again run `sudo update-grub` in a console window. At that point, next time you reboot, you should have a much tidier boot menu with the desired Windows partition at the top and the Linux partitions following them.

Just to be clear, this process only works with grub 2\. Legacy systems running the older version of grub have to be handled differently. There is plenty of additional information on the web. For example, [http://www.dedoimedo.com/computers/grub-2.html](http://www.dedoimedo.com/computers/grub-2.html "Link to grub2 tutorial"), has a nice top level description of how grub 2 works and lots of examples of special situations you might want to handle.

Here's to tidy menus.