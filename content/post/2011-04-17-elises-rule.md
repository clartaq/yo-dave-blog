---
title: Elise's Rule
tags:
  - hardware
id: 353
comment: false
categories:
  - pcs (and macs)
date: 2011-04-17 01:09:53
---

Based on repeated observations, my wife, Elise, has formed a simple rule:
> When Dave has any extra time off, the computer will be broken -- big time.
It's _freaking uncanny_.

<!--more-->When my wife says "the computer", she means the dual-boot Windows/Linux box she usually works with. It's a relatively new Falcon Northwest system with a slightly overclocked i7 processor with liquid cooling, 16GB of RAM, a fast video card driving a 1920x1200 24" monitor, USB 3, a 1TB drive on a SATA III connection, running Windows 7 Professional (64-bit) and Ubuntu 10.10 (64-bit). The keyboard is a Logitech G110 with illuminated keys, which I truly adore. The mouse is a Logitech G500\. In addition, because of my paranoia about its stability, it has an additional 2TB of internal drives and 3TB external drives that I use for incessant backups. It's really a very sweet system if it weren't for this continuing problem.

Let's talk about the most recent episode. While taking a couple days of vacation for my birthday, I was going to install [MikTeX](http://miktex.org/ "Link to miktex site") to create some nice [$\LaTeX$](http://www.latex-project.org/ "Link to the LaTeX project web page") documentation for a molecular dynamics simulation I've been working on.

### Networking Failure

Boot 'er up and the network connection fails. Well, Ok. This has been happening on and off for the past few weeks. Sometimes you have to reboot to get it to work. But this time rebooting doesn't help. During start up, the connection light on the network switch (either one of them) goes dark. The connection at the PC has an amber light that flashes occasionally but no connection. And the network monitor gadget on the PC is flat lined. The Windows Device Manager says the device is working properly. I uninstall and re-install the networking drivers just to make sure. No joy.

Ok, reboot into Linux. Oh oh. No connection, even on Linux. That's never happened. I unplug from the PC, plug it into the Mac and everything works fine. Back to PC - _nada_. Taking off one of the side panels on the PC allows me to watch the progression through the boot process by following a series of LEDs on the motherboard. It's all good.

I order a new network card and keep watching the boot up while waiting for delivery  until it stops booting at all.

### Boot Failure

Now it can't make it through the boot process in either Windows or Linux. Windows blue screens partway through the boot. Linux gets a kernel panic. Well that's new too. Cold boot. Warm boot. All fail.

Eventually, looking into the BIOS settings reveals that the CPU frequency is _way_ off. Some of the other settings are different from the state that it was delivered in too. I'm not an overclocker. I only got this system overclocked in the first place because of Falcon's reputation for making stable systems. I don't even know _how_ to change the CPU frequency.

### The Unwind

Luckily, the motherboard BIOS has a dead simple way to do some automated overclocking and unclocking, returning the system to "stock" settings. Then I installed the Intel Extreme Tuning Utility that came on one of the startup disks. That lets me set things back to the original condition listed in the system documentation. Booting seems to work.

The network card I ordered earlier arrives. I disable the on-board networking stuff, plug in and connect the new card. Install the drivers. Nothing on either Windows or Linux. Move the card to a different PCIe slot. Nothing. This time the light at the network switch continues to show the connection, but it doesn't work. The Windows Device Manager says things are groovy, but obviously lies.

I pull the new card out, pack it up for return, re-enable the on-board LAN goodies, plug it in and it works! Who knows why? And it has worked consistently since - about 24 hours now.

### Next Up - Invisible Drives

Things are working nicely again until Linux does an update. The boot now will not complete. Revert to an old distribution and boot works, but it can't see all of the drives. In particular, the multi-boot does not see the Windows drive. It is visible to the BIOS but not to fdisk. Needless to say, the Grub multi-boot fails to boot into Windows.

Setting up the BIOS to boot directly to the Windows drive brings Windows up successfully. Linux just can't see it.

### Conclusion

And that is the situation as I write this. I don't really know why the networking failed but I suspect a physical problem with the socket on the motherboard. The boot failure and overclocking? No idea at all. The problem with drive recognition probably has something to do with the goofy Ubuntu update, but that's a fix in progress.

I don't _think_ I'm a complete dufus, but I'm having my doubts.

So what now? Oh, yeah. $latex \LaTeX$ and that documentation I wanted to write.

**Update**: My company gave us Good Friday off as a holiday. Like clockwork, we lost networking. Networking. That's a story for another post.