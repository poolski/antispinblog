+++
author = "Kyrill"
categories = ["Tech", "Help", "Windows Server", "Xen", "XenServer 7"]
date = 2017-06-13T14:51:58Z
description = "This may help you recover  from corrupt I/O drivers/Management Engine in Windows Server 2012  on Citrix Xen Server which can break after rebooting."
draft = false
image = "https://ap-content.storage.googleapis.com/2017/062017/06/pexels-photo-260397.jpeg"
slug = "recovering-windows-server-2012-on-xen-server"
tags = ["Tech", "Help", "Windows Server", "Xen", "XenServer 7"]
title = "Recovering Windows Server 2012 on Xen Server."

+++

Greetings!

In this post I want to cover what to do when Windows Server 2012 decides to corrupt its Xen I/O drivers, paravirtualisation drivers, thus rendering the Citrix Xen Management Tools unable to communicate with their host server. 

I've yet to figure out why Windows does this - I've already lost one server to it before I managed to fix the second to succumb to this problem. 

###Symptoms:

* Unresponsiveness to reboot commands from CLI or XenCenter.
* Needing to force reboot/shut down affected VM.
* Affected VMs take up to 20 minutes to boot and show the login screen.
* Login takes anywhere between 10 minutes and an hour.
* You're unable to launch any processes whatsoever - no Task Manager, Windows Explorer, etc. 

Basically, in this state, nothing works and it's impossible to reinstall the XenServer tools because the Xen storage drivers are FUBAR so even though `guest-tools.iso` mounts, you can't do anything with it. 

Usually, this is compounded by services failing to start. This includes the Windows Installer Service (`MSIServer`) which makes it even harder to fix the problem.

###My Solution

Turns out not all is lost. 
All you need is:

* A working Windows 10 ISO image. Any flavour will do. 
* A working copy of Citrix XenCenter on a Windows machine, connected to your hypervisor/pool. 
* Local administrator access to your VM.

**Step 1: Enable and boot into Safe Mode**

1. Mount the Windows 10 ISO image into the affected VM. 
It doesn't matter what state the VM is in as we'll likely end up force rebooting it.
* Reboot the VM. You'll likely need to force reboot it using either XenCenter or by issuing `xe vm-reboot --force vm=vm_name_here` from the CLI.
* As it's rebooting, watch the console for the *press any key to boot from DVD* message. Press a key.
* Once the Windows 10 installation screen comes up, click **Repair your computer** in the bottom left corner. 
* Once you get to the repair screen, click **Troubleshoot**.
* On the Troubleshooting screen, click **Advanced options**
* From there, click **Startup Settings**
* Select option 4 - **Enable Safe Mode**
* Continue and restart. 

**Step 2: Reinstall Xen Tools**

Once you boot into Safe Mode, you should be able to actually *do* stuff with your machine. 
First things first - you need to configure Windows to allow the Windows Installer Service (MSIService) and Citrix Management Agent (XenSvc) to start in Safe Mode. They're blocked from doing so by default.

To do this, you will need to make some modifications to the Registry.

1. Open the Registry Editor. Hit Win+R and type `regedit` in the box or fire up the Start Menu and run `Registry Editor`. 
* Browse to `HKLM\System\CurrentControlSet\Control\SafeBoot\Minimal`, assuming you booted into regular Safe Mode. If you booted into Safe Mode with Command Propmt, you'll need to edit `Miniman` and if you booted into Safe Mode with Networking, you're looking for `Network`.
* Right click on the right folder (assuming `Minimal` here) and select `New` and `Key`. 
* In the Name field, type `MSIServer`.
* Open the new key, double click on the `(Default)` entry and enter `Service` in the value. Uppercase. 
* Create another key for `XenSvc` and do the same as above. 

Now you need to remove the previous broken version of the Xen Tools. Head to your Control Panel and **remove** the Citrix Xen Management Tools. 
**Don't reboot, though**.

Once that's done, you can mount the `guest-tools.iso` image in your machine from XenCenter. 

Open up Windows Explorer, browse to the DVD drive and run `Setup.exe` as Administrator for good measure (right click > Run As Administrator). It should run through the setup without any issues and complete. 

Once it's done, you can unmount the disk and reboot - everything should come up as normal. 

Please leave a comment and share if this has helped you.
