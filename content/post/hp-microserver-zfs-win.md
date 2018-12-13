+++
author = "Kyrill"
categories = ["Computers", "Featured", "fullwidth", "Servers", "Tech", "Tutorials", "ZFS"]
date = 2012-05-02T17:10:34Z
description = ""
draft = false
image = "https://ap-content.storage.googleapis.com/2012/05/521986_599137269807_285601433_2567868_1327492712_n.jpg"
slug = "hp-microserver-zfs-win"
tags = ["Computers", "Featured", "fullwidth", "Servers", "Tech", "Tutorials", "ZFS"]
title = "HP MicroServer + ZFS = Win"

+++


I recently procured one of these beauties after staring enviously at the one we have on our desk for testing at work.

![](https://antisp.in/blog/wp-content/uploads/2012/05/521986_599137269807_285601433_2567868_1327492712_n.jpg)

It’s a [HP ProLiant MicroServer](https://www.ebuyer.com/281915-hp-proliant-turion-ii-n40l-microserver-100-cashback-658553-421) – a bit of a mouthful, but a good piece of kit! It comes with

- 2GB RAM
- 250GB HDD
- Dual-core AMD Turion II N40L processor
- Gig Ethernet
- More USB ports than I know what to do with
- Three more 3.5″ disk bays, which I filled with 2TB Western Digital disks
- Some flashy lights

I bought it after umm-ing and aah-ing about building a replacement for my ageing Windows Home Server file server. It’s huge, cumbersome, slow and, crucially, *noisy. *The case and CPU fans are so old and decrepit that I can hear it down the hall with my door shut. That, combined with the fact that the device has started to crash, reset and fail to boot on a regular basis meant it was time to upgrade/replace/destroy/purge (delete as appropriate).

So I got this thing after hearing rave reviews of how easy they are to run. Having populated it and powered it on, I found that it was so bloody quiet that if the little green light wasn’t switched on, I’d have no clue it was on in the first place…

<figure class="thumbnail wp-caption aligncenter" id="attachment_1565" style="width: 498px">
![](https://antisp.in/blog/tech/restoring-from-snapshots-with-zfs/ "Restoring from snapshots with ZFS")
, so that’s what I went with, especially after seeing this graph:

![](https://antisp.in/blog/wp-content/uploads/2012/05/iops-perfom1-480x327.jpg)

Even though this measures performance against Solaris Express, based on the data here, OpenSolaris would wipe the floor with FreeNAS.

The installation went by without a hitch! The unit quickly DHCP’d itself an address which is fine by me. Once that was done, it was time to apply updates and reboot – again, no worries whatsoever.

While this was going on, I stumbled across [napp-it](https://napp-it.org/), a NAS distro. It turns out that it can also serve as a frontend to OpenIndiana. While I was happy to administer the server from commandline, I figured that it wouldn’t hurt to have a graphical interface for when I was feeling lazy.

Installation was, again, ridiculously easy:

 

> wget -O - www.napp-it.org/nappit | perl

After about 10 minutes of the unit Doing Things under the hood, I found myself with a brand spanking new installation of napp-it!

Creating the storage pool from the three 2-terabyte disks was a doddle too! So now I have 3.56TB of space with the three disks in a RAID-Z pool. This means that one of the three disks can fail and I won’t lose All The Things. This fills me with a great sense of peace.

Also, the snapshotting capability of ZFS means I can back up important data on a regular basis, with an option of copying those snapshots to a remote server for added security!

Now, to copy 1.4TB of data from three (possibly four) NTFS-formatted disks that WHS used for its storage pool…

 


