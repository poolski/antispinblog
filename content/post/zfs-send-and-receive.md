+++
author = "Kyrill"
categories = ["Featured", "fullwidth", "Tech", "Tutorials", "ZFS"]
date = 2012-04-12T09:38:33Z
description = ""
draft = false
image = "https://ap-content.storage.googleapis.com/2012/04/zfs-banner.png"
slug = "zfs-send-and-receive"
tags = ["Featured", "fullwidth", "Tech", "Tutorials", "ZFS"]
title = "ZFS Send and Receive"

+++


As I mentioned in [a previous post](https://antisp.in/blog/tech/restoring-from-snapshots-with-zfs/ "Restoring from snapshots with ZFS"), I’m running a series on ZFS storage systems mostly for the purposes of remembering this stuff and tangentially on the off chance that it’ll help someone too!

Today’s documentation entry is concerned with using ZFS’ *send* and *receive* commands to backup datasets over a network.

Since installing a new storage server at work, we’ve had an old DL385 G6 chassis with 6 1TB disks kicking about for a good 6 months with a view to converting it to a backup storage device for snapshots and the like. We finally got around to upgrading the box with some fresh RAM and adding some SSD disks to store the OS and to speed up the cache.

After configuring networking, setting up a mirrored root pool and creating the storage pool, it’s time to get down to the (surprisingly easy) process of shunting datasets around.

### Network Cats

The whole dataset replication process is laughably easy, requiring two ZFS commands: *send *and *receive*. To send data over a network to a remote machine, we’ll need to pipe it through a network stream. You can do this in two ways:

<table cellpadding="5px" cellspacing="3px;" class=" table table-hover" style="border: 1px solid #dcdcdc;" width="100%"><tbody><tr><td></td><td>**Pros**</td><td>**Cons**</td></tr><tr><td>**SSH**</td><td>- Your data is encrypted as it’s sent over the network, so it becomes much harder to intercept. Good for true offsite backup
- SSH keys provide an added layer of security and potentially eliminate the danger of a third party guessing the password for your SSH account.

</td><td>- Slower than Netcat streams due to needing to encrypt the data stream at one end and decrypting it at the other. If sending large amounts of data, this can add time to the transfer.

</td></tr><tr><td>**Netcat**</td><td>- Much faster than SSH as your traffic is sent as-is from one server to the other without any encryption. Good for internal LANs and secure networks.

</td><td>- Much less secure than an SSH tunnel – not recommended for use over an unsecured network. i.e. The Internet.

</td></tr></tbody></table>In my case, I’m backing up over an internal LAN so netcat is plenty.

If you don’t have netcat installed, use this to quickly nab it:

> # pkg install netcat

### Never Cross The Streams

The process is very easy to set up. In my case, each dataset is snapshotted regularly by a cron job and I want to preserve the snapshots alongside the main dataset.

> zfs list -t snapshot
> 
> ... tank/data@day_02 46.4M - 73.7G - tank/data@day_03 18.1M - 73.7G - tank/data@day_04 16.1M - 73.7G - ...

First things first: set up the *receiving* server to listen for traffic and tell it what to do with whatever comes out the other end:

> root@receiving# nc -l -p 8080 | zfs receive -vd tank

***nc -l ****-p 8080 *****to set up netcat in listen mode on port 8080, pipe ( ***| ***) output from that to ***zfs receive -vd tank ***which tells ZFS to put any data it receives into the /tank pool, with the ***-vd ***options which output verbose information to the terminal and preserves the dataset names from the sending server, enabling you to send over multiple snapshots.

Now to send. If you have multiple snapshots of your dataset(s), it pays to create one before starting the send so you have an easy reference point for the incremental sends.

> root@sending# zfs snapshot tank/data@latest

Now you’re ready to send.

> root@sending# zfs send -R tank/data@last | nc receiving.example.com 8080

This will recursively send the dataset *tank/data *along with all the snapshots leading up to data@last over to your remote server through netcat over port 8080 which we configured on the receiving server above.

Now make a cup of tea and wait a minute or a day, depending on your dataset size.

That’s it!


