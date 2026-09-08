---
title: "Arch Ubuntu Dualboot: Boot Loader Conflict \U0001F6AB \U0001F4BB"
summary: "Ubuntu has declared a dictatorship and it's probably my fault for not setting political safeguards"
date: 2026-06-28
draft: true
tags: ["os"]
showToc: true
TocOpen: false
ShowReadingTime: true
ShowBreadCrumbs: true
UseHugoToc: true
---

*This happened back on the 26th but I have been working and have a laptop so today is the day to tackle it. This will basically act as a live journal*

## TL;DR

TODO

## What happened?

Ok, so. I have been running Arch Linux for the last 8-ish months without issue, surprisingly. The rare hiccup has been solved every time in less than an hour thanks to the sheer depth of the Arch Linux wiki, and my system has been customized to my liking. Sadly though, Arch does not provide everything that I need.

I am still in the process of completing my masters, nearing it's end this coming January. Currently, I am reaching the project portion of my self-guided study on robotics, and I am returning back from writing my first paper to complete another experiment and write a final thesis paper. Due to both these responsibilities, I need access to a few specific platforms, namely [Unity](https://unity.com/) for VR environment creation and I/O handling, and [ROS2](https://www.ros.org/) for managing physical and simulated robotics and cameras. Both of these have *specific and niche ways* to run on Arch, but run without issue on Ubuntu, which I used previously and continue to use in the lab. I have an entire SSD not being utilized currently, surely I can carve out 500GB to run Ubuntu 22.04 and just add it to the Arch boot loader, right?

**WRONG!** Ubuntu has sadly taken over all booting responsibilities for my desktop and is making it a challenge to even access my Arch environment. Ubuntu required a GRUB installation spot, which I put in the same space it was installed in thinking it would act as its own option to boot in the BIOS, but it has overstepped my expected bounds. I'm happy to throw some dramatics in here for effect but honestly if I lose access I can probably still pull data from the hard drive, and if I lose data, it's fine I'm responsible with my backups (or realistically Arch is). 

My goal right now is to resume using Arch's boot loader and just fully uninstall GRUB altogether. I have almonds, water, and instant ramen, let's see how this goes.

### What are you listening to?

Why wasn't this your first question? This is the crux to the whole solution.

I spent the first half of today walking around car-less, so I have already been going through a bunch of albums. Currently, I've made my way through some [Fleshwater](https://www.fleshwater.fm/), [Julie](https://myantiaircraftfriend.com/), and [They Are Gutting A Body of Water](https://theyareguttingabodyofwater.bandcamp.com/) (which I also love to refer to as their acronym tagabow). The wise minds of Rate Your Music chart these mainly as shoegaze slacker rock with a little bit of hardcore labels in there, but I group them together as albums that would fit seamlessly in the PS2 hit ATV Offroad Fury (specifically 3, if you were wondering). I'm diving through the charts currently and I note that there's a ton that I recognize as classics I should go through and I've spent like 15 minutes previewing albums but we're gonna settle on [Unwound's 1996 album Repetition](https://unwound.bandcamp.com/album/repetition)

## Background research

### Arch Bootloader

During my install, I went with the highly suggested `systemmd-boot` which has served me fine as Arch is the only distro I was running at the time. While reading up about it [on the wiki](https://wiki.archlinux.org/title/Systemd-boot), I have realized something that may hinder my plans:

> Note that systemd-boot can only start EFI executables (e.g., the Linux kernel EFI boot stub, UEFI shell, GRUB, or the Windows Boot Manager) from the EFI system partition it is installed to or from an Extended Boot Loader Partition (XBOOTLDR partition) on the same disk. 

These systems are currently running on different disks. However, according to [this random forum answer I found](https://forum.endeavouros.com/t/systemd-boot-windows-on-different-drive/56658/3), we should be able to flip some flags and be fine. This is a concern for later once I get back into Arch.

### Ubuntu Bootloader

Ubuntu by default runs `GRUB`, which I'm honestly quite familiar with from running Ubuntu/Debian for a number of years in different contexts, but I was not aware of the grasp it takes on the system. `GRUB` also uses `efibootmgr`, which seems to be the tool that consistently overwrites my BIOS boot order. It has also set both its own `ubuntu` boot option, as well as a `UEFI OS` option, both pointing to the Ubuntu installation and completely removing any note of my Arch installation on my other drive.

## Actions taken

### Previous hasty attempts

My first step was to see if I could disable `GRUB`, which honestly I'm glad didn't work because I think I would be in more trouble. I ran some `dpkg` and `dpkg-reconfigure` commands on existing `grub-efi-amd64-*` packages, trying to change and/or remove them, but dependencies stopped me. The next step was to stop `efibootmgr` from editing by BIOS boot order, to try to boot the `UEFI OS` option first. This was done using `efibootmgr`, seeing the options, and setting `UEFI OS` as the target to boot. As mentioned previously though, this was done in vain as it also points to the Ubuntu installation. Also, `efibootmgr` keeps adding `ubuntu` entries on top of the existing one if this is done, which is neat.

### Adding Arch to GRUB

Next step is likely to try to add my Arch installation to `GRUB`. First thing's first though, `GRUB` isn't even showing up. Let's change that. In the `GRUB` configuration file located at `/etc/default/grub`, we'll do the following:

- Comment out the line `GRUB_TIMEOUT_STYLE=hidden`
- Set `GRUB_TIMEOUT=5`
- Save and run `update-grub` to apply changes

Now when we restart we get to see the `GRUB` menu and all its glorious options! Great! Now, how do we find an OS on a different drive with `GRUB`? First, we can enable `GRUB`'s OS probing feature by adding the line `GRUB_DISABLE_OS_PROBER=false` to our previously mentioned configuration file, but doing this does not add any entry for our Arch drive.

It seems like the drive may not be mounted in Ubuntu, so to fix this I ran `fdisk -l` to remind myself what it was labelled as, mounted the drive using `sudo mount /dev/x /mnt`, but it is noted to already be mounted. The next logical step is just to make the entry myself and see if we can get it to work. Honestly, I spent the next 30 minutes playing with possible entries in `/etc/grub.d/40_custom` and nothing was working. I think I need to get deeper into `GRUB`

### Meeting GRUB in the command line

Through the power of pressing `c` during `GRUB`'s booting process, you can access the command line. First, I want to find my Arch partition, and running `ls` to see the partition names, and then `ls (x,y)/`, I can sort my way through.

I found my partition but this was also painfully slow and I am now in a reckless mood, what if we did replace `GRUB` with `systemd-boot`, would that be able to find its kin?

### Get back in here `systemd-boot`

Thankfully there's an [entire guide for my specific Ubuntu install](https://gist.github.com/gdamjan/ccdcda2c91119406a0f8d22f8b8f2c4a), isn't the internet great?

The internet is in fact, terrible, and I have now boot-looped my system.

## End result

[REDDIT](https://www.reddit.com/r/archlinux/comments/17ejfon/broke_and_fixed_my_arch_system/)

https://forum.endeavouros.com/t/chroot-into-a-btrfs-uefi-system-from-live-media/15986/3


### How was the album?