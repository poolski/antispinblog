+++
author = "Kyrill"
categories = ["fullwidth", "Internet", "Linux", "Tech", "Tutorials", "Ubuntu"]
date = 2012-05-24T10:43:25Z
description = ""
draft = false
image = "https://ap-content.storage.googleapis.com/2012/05/PlmRC.jpg"
slug = "how-to-fix-thinkpad-x121e-3g-bluetooth-in-ubuntu"
tags = ["fullwidth", "Internet", "Linux", "Tech", "Tutorials", "Ubuntu"]
title = "How to fix ThinkPad x121e 3G & Bluetooth in Ubuntu"

+++


I got tired of trawling through forums to find these answers so I collated them for you!

My work laptop is a Lenovo ThinkPad x121e. It’s stupidly energy-efficient, quiet and most importantly built like a brick. When I took the bottom cover off to upgrade the RAM to 8GB, I was struck by the build quality and was gratified to see the area around the hard disk and other crucial sections of the mainboard was reinforced with extra structural metalwork.

<figure class="thumbnail wp-caption aligncenter" id="attachment_1594" style="width: 498px">
![](http://antisp.in/blog/wp-content/uploads/2012/05/IMG_20120524_1047161.jpg)
<figcaption class="caption wp-caption-text">It feels like it’s designed to be used out in the Real World.</figcaption></figure>I ripped out the 256GB magnetic disk and stuck in a Samsung 64GB SSD on which I installed Ubuntu 12.04. With a Core i3, 8GB of RAM and a SSD, this thing *flies. *

**However, I soon ran headlong into a [known issue](https://bugs.launchpad.net/ubuntu/+source/linux/+bug/812866): the builtin 3G mobile internet card and Bluetooth connector arbitrarily become hard blocked if you hibernate the machine or if the battery runs out of charge.

There’s no rhyme nor reason for this, from what I could discern. The F5 function key, which controls the wireless cards (Bluetooth, WiFi & 3G) doesn’t work in Ubuntu, so re-enabling any of them was impossible with this method. Some suggestions I came across online:


## rfkill

*# rfkill list*

*0: tpacpi_*<wbr></wbr>*bluetooth_*<wbr></wbr>*sw: Bluetooth  
 Soft blocked: yes  
 Hard blocked: yes  
 1: phy0: Wireless LAN  
 Soft blocked: no  
 Hard blocked: no  
 2: wwan: Wireless WAN  
 Soft blocked: yes  
 Hard blocked: yes *

*# rfkill unblock all*

Which should’ve unblocked everything. Unfortunately, *rfkill *only soft unblocks devices. As Ubuntu thinks that the devices are *hard blocked, *rfkill is useless.


## BIOS Update

The other suggestion is to update the BIOS on the laptop as that’s claimed to fix this issue. However, if you hadn’t kept your old magnetic disk like I had, you’ll need to get your hands on a USB CD drive as the ThinkPad x121e lacks an optical drive. Then you can download the BIOS update live CD from the Lenovo website and run the upgrade which will fix it ***until you hibernate the machine or lose battery power***. After that happens, you can’t update the BIOS again as you’ll already be at the latest version, so you’re kind of screwed.


## Reset BIOS to defaults

This is the method that I now use to recover from the 3G/Bluetooth failure.

You will need:

- Your laptop, preferably plugged into AC power
- A USB stick with an Ubuntu 12.04 <span style="text-decoration: underline;">alternate install</span> live image. Don’t use the 12.04 Desktop image as it doesn’t contain rescue mode.
- 15 minutes.

Firstly, make sure that the USB stick is bootable and works fine – run through the rescue mode steps but don’t change anything to make sure it’s working.

1. Power off your laptop & power it back on again.
2. As it’s booting, hit Enter at the Lenovo ThinkPad logo
3. Hit F1 for the BIOS
4. Navigate to the last tab and select “Reset to defaults”. **Remember to make a note of any custom settings you had set up. **
5. Save and exit.

If your BIOS was configured to use the legacy MBR mechanism to boot, things should just *work*.

If your BIOS, like mine, is set up to use the newer EFI tech, you’ll see a “No bootable device found” error when you boot.

**Don’t panic**.

1. Plug in your live USB stick
2. Boot into *rescue a broken system* mode and follow all the steps until you get to the ‘Select device to mount as root’ step.
3. If you let the Ubuntu installer set up its own partition map, you’ll have /dev/sda1 containing the EFI partition, /dev/sda2 containing your / (root) partition and /dev/sda2 containing swap.
4. Select /dev/sda2 as your root partition and run a shell from that partition (not from the live installer).
5. Type these things in:

*/bin/bash* (this will load the *bash* shell that support autocomplete and a bunch of other user useful things)*  
 mount /boot/efi  
**grub-probe –target=device /boot/efi/EFI/ubuntu/grubx64.efi*

You should get ‘/dev/sda1’ as the response from that command. If  you don’t, make a note of which device the EFI partition is on. Then type this command in, substituting wherever your partition is for ‘–part 1’ e.g. ‘– part 3’ if your EFI partition is found at /dev/sda3.

*efibootmgr –create –gpt –disk /dev/sda –part 1 –write-signature –label “GRUB2” –loader ‘\EFI\ubuntu\grubx64.efi’*

You can choose whatever label you want. This is what will show in the BIOS boot menu if you bring it up. It’s not important.  
 This will recreate the record in the EFI ‘list of bootable Things’ stored in NVRAM that’s nuked when you reset the BIOS.

That’s it! Please share this with people who are having the same problem.


