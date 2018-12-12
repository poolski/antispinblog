+++
author = "Kyrill"
categories = ["fullwidth", "IT", "Tech", "Tutorials", "ZFS"]
date = 2012-02-08T12:49:04Z
description = ""
draft = false
slug = "restoring-from-snapshots-with-zfs"
tags = ["fullwidth", "IT", "Tech", "Tutorials", "ZFS"]
title = "Restoring from snapshots with ZFS"

+++


I’ve been using ZFS at work for a while, but haven’t really had time to tinker with it to see what makes it tick and use it actively. One of the great benefits that I’ve found to using it is that it sits there and requires no human input 99.9% of the time. It *just works. *Which makes me happy.

Today, though, I had to Do Stuff. I’m posting this mostly as a reminder to myself on how to do stuff and on the off chance that Google indexes this page and others of its ilk under ‘ZFS Tutorials’ or ‘ZFS How-To’ or something.

### Snapshots

ZFS uses snapshots to record the current state of a given dataset. Snapshotting creates very little CPU and storage overhead, making it a useful backup method, especially if you have  CRON job set up to regularly snapshot your important data. You can also manually snapshot a dataset:

> zfs snapshot tank/data@080212

This will create a snapshot of your dataset (here called ‘data’) with today’s date appended to it. You can use anything after the @, but some identifiable date/time string is advisable.

If you want to see the snapshot:

> zfs list -t snapshot
> 
> tank/data@day_02 46.4M - 73.7G - tank/data@day_03 18.1M - 73.7G - tank/data@day_04 16.1M - 73.7G - tank/data@day_05 16.0M - 73.7G - tank/data@day_06 16.0M - 73.7G - tank/data@day_07 18.7M - 73.7G -

It’s a good idea to use the *-t snapshot* switch to just view snapshots instead of showing all  of your datasets.

### Restoring files from a snapshot

Sometimes, you will need to recover lost or deleted files from an earlier backup. You can either opt to restore an earlier snapshot of the current dataset or if you want to recover specific files, you can do this:

> zfs clone tank/data@day_02 tank/data_restore cp -Rv /tank/data_restore/some_folder /tank/data

That’s all.

More to come as I figure out what other goodies ZFS has in store

 


