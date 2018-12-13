+++
author = "Kyrill"
categories = ["fullwidth", "Tech", "ZFS"]
date = 2014-07-12T13:52:41Z
description = ""
draft = false
image = "https://ap-content.storage.googleapis.com/2014/07/ZFS_orange_by_setuid.png"
slug = "tips-setting-home-nas"
tags = ["fullwidth", "Tech", "ZFS"]
title = "Tips for setting up your home NAS"

+++


After a conversation with a friend about the best way to go about setting up a NAS for your home, I thought I’d relate some of the things I’ve learned over the few years that I’ve been working with ZFS at home and at work.

### Specs

If you’re going to be building a homebrew NAS box to use as your media/file storage, streaming server and porn dump, you’ll need to think about what sort of kit you need. I’m going to be assuming an [OpenIndiana-based setup](https://antisp.in/2012/05/hp-microserver-zfs-win/ "HP MicroServer + ZFS = Win") similar to what I currently run.

#### Easymode

Buy a [HP ProLiant Microserver Gen8](https://www.ebuyer.com/517761-hp-proliant-gen8-g2020t-microserver-712318-421). The Gen7 are also decent bits of kit, but these units support more memory, have a faster CPU and are better-built and generally More Better.

#### DIY

You need a couple things:

- A decent CPU. It doesn’t need to be blazing-fast since it’ll be doing mostly a lot of nothing 95% of the time. A moderately cheap i3 or i5 will probably do the trick.
- RAM. Lots of RAM. As much as you can afford/the motherboard will support/you think is necessary. 8GB minimum. The reason for this is that ZFS loves RAM. The more memory it has, the more data it can load into RAM, meaning faster IO for you.
- Disks. You want to get disks with as good IO performance as you can, since having disks that have shitty IOPS will make your NAS crawl.
- SATA/SAS controller. Don’t buy a RAID card. You won’t need it. ZFS paired with a hardware RAID card can result in epic data corruption as the RAID card actually interferes with ZFS’ own data integrity magic and can lead to hilariously broken arrays. If you have a RAID card, set it to JBOD mode or the equivalent. ZFS needs direct access to the disks without a RAID controller being in the way.

### Software

I personally run OpenIndiana on my MicroServer and it’s been rock-solid for years now. I’d recommend it. You can get it [here](https://openindiana.org/). Don’t waste a whole disk for installing it on, however, especially if you have a limited number of disks you can use.

The beauty of ZFS-based systems is that the OS is actually trivial. You care about your *data*, primarily. If the OS dies, you can reinstall, import your pool and you’re back up. With that in mind, I would install the OS on a USB stick or SDCard on your motherboard, if it supports it. This way you have the maximum number of available disks to use for storage.

### Disk Config

Here, you have two options:

#### Use a set of mirrors.
```
capacity operations bandwidth pool alloc free read write read write 
------------------------- ----- ----- ----- ----- ----- ----- 
virt 1.89T 3.54T 0 1.60K 15.3K 51.3M 
  mirror 324G 604G 0 267 0 8.27M 
    c4t5000C50041FDDFEBd0 - - 0 147 0 8.27M
    c4t5000C50041FDE0DFd0 - - 0 146 0 8.27M 
  mirror 323G 605G 0 168 0 7.78M
    c4t5000C50041FDEA1Fd0 - - 0 88 0 7.78M 
    c4t5000C50041FDEB2Fd0 - - 0 87 0 7.78M
  mirror 323G 605G 0 217 0 9.10M 
    c4t5000C500426D38D3d0 - - 0 118 0 9.10M
    c4t5000C500426D3417d0 - - 0 118 0 9.10M
  mirror 323G 605G 0 206 15.3K 7.60M
    c4t5000C50057F93F57d0 - - 0 80 0 7.60M 
    c4t5000C50057FA2D6Fd0 - - 0 83 15.3K 7.60M
  mirror 323G 605G 0 295 0 7.69M 
    c4t5000C50057FA2DC3d0 - - 0 127 0 7.69M
    c4t5000C50057FA28C3d0 - - 0 133 0 7.69M 
  mirror 323G 605G 0 226 0 7.11M
    c4t5000C50057FA273Fd0 - - 0 142 0 7.11M 
    c4t5000C50057FA2847d0 - - 0 143 0 7.11M
```
This sort of setup gives you expandability further down the line, so if you decide you only need 4TB of storage, you can create a stripe of two mirrors of 2TB disks initially. If you decide that you need to add an extra 2TB of storage, you simply add a third mirror of 2TB disks, etc, etc. This gives you great flexibility and decent read/write performance to boot.

#### Use a RAIDZ*n*

If you aren’t worried about your IO performance being blazing fast, you can instead opt for a RAIDZ1 or RAIDZ2* *which is the equivalent of a RAID5/RAID6. If you have a limited number of usable disks – for price or other reasons, this is often a good alternative. It will still provide you with some redundancy – you can lose a disk and keep going, providing you replace the failed drive sooner rather than later.

The drawback is that you can’t expand a RAIDZ pool as easily as ZFS doesn’t support adding disks to a RAIDZ. You can increase the size of the array by sequentially swapping disks out for larger ones – e.g. swapping your 2TB disks for 4TB disks – and letting the pool resilver (rebuild) on to the new drives. Once all disks are replaced, the pool will expand to the full capacity of your drives.

Hope this helps answer some questions about how best to set up your home NAS


