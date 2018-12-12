+++
author = "Kyrill"
categories = ["fullwidth", "Internet", "Servers", "Tech", "Thoughts"]
date = 2012-05-09T15:10:05Z
description = ""
draft = false
slug = "ubuntu-12-04-on-rackspace-cloud-servers"
tags = ["fullwidth", "Internet", "Servers", "Tech", "Thoughts"]
title = "Ubuntu 12.04 on Rackspace Cloud Servers"

+++


Really, the title should have some sort of clever warning about not upgrading 11.10 to 12.04 when running a Rackspace Cloud Server.

I tried this on Saturday (4 days ago) and it’s taken me this long to recover my server. And by recover I mean ‘snatch the files and data off it, then recreate it from scratch running 11.10’. You see, among other things, Ubuntu seems to require a custom kernel to run on Rackspace’s infrastructure. Tied into that kernel is also the Remote Console feature.

Unlike SliceHost, the remote KVM is dependent on the kernel being bootable (which is pointless in most situations where you’d need it as your machine is probably NOT bootable when you DO end up needing the remote KVM feature). So once I completed the upgrade with the requisite kernel upgrade and package updates, the server rebooted and stayed offline for the next 4 days.

Protip: **Don’t upgrade your Ubuntu release when running a Rackspace Cloud Server. **


