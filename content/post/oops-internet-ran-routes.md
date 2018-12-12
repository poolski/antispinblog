+++
author = "Kyrill"
categories = ["fullwidth", "Internet", "Networking", "Tech"]
date = 2014-08-13T10:22:05Z
description = ""
draft = false
image = "https://ap-content.storage.googleapis.com/2014/08/Selection_044.png"
slug = "oops-internet-ran-routes"
tags = ["fullwidth", "Internet", "Networking", "Tech"]
title = "Oops. The internet ran out of routes"

+++


Yesterday, internet service providers around the world and the services that used their networks started going offline and experiencing abnormal levels of packet loss and latency. The issue was widespread and affected a great many services. What happened?

The internet ran out of routes.

**Update: **

Here is a more thorough explanation of the issues of a couple of days ago: [http://www.bgpmon.net/what-caused-todays-internet-hiccup/](http://www.bgpmon.net/what-caused-todays-internet-hiccup/)

The IPv4 public address space (the available publicly-routable IP addresses) has become more and more fragmented as companies have broken down the address blocks they own more and more and sold small chunks of their IPs on. Each distinct IP block that is connected to the internet needs to be routed to and from by routers all over the world and so requires an entry in the global <abbr title="Border Gatway Protocol">BGP</abbr> routing table. This is data stored by routers that ISPs operate.

Most routers, especially older pieces of hardware, have a limit of 512,000 odd routes that they can hold in their global routing table. This limit was mostly defined by the available memory the router had and when IPv4 was conceived, 512,000 routes seemed like a ludicrously high number.

As a new address block is carved off in the IPv4 address space, another route is advertised for that netblock. Yesterday, we exceeded the hard limit of 512k routes that most routers could hold.

<table border="0" cellpadding="0" cellspacing="0" class=" table table-hover"><tbody><tr><td valign="top"><table border="0" cellpadding="0" cellspacing="0" class=" table table-hover"><tbody><tr><th align="left">Date</th><th align="right">Prefixes</th><th align="right">  CIDR Aggregated</th></tr><tr><td>06-08-14</td><td align="right"><tt>  511103</tt></td><td align="right"><tt>  280424</tt></td></tr><tr><td>07-08-14</td><td align="right"><tt>  511297</tt></td><td align="right"><tt>  280432</tt></td></tr><tr><td>08-08-14</td><td align="right"><tt>  511736</tt></td><td align="right"><tt>  280442</tt></td></tr><tr><td>09-08-14</td><td align="right"><tt>  511719</tt></td><td align="right"><tt>  280722</tt></td></tr><tr><td>10-08-14</td><td align="right"><tt>  511762</tt></td><td align="right"><tt>  280563</tt></td></tr><tr><td>11-08-14</td><td align="right"><tt>  511719</tt></td><td align="right"><tt>  280860</tt></td></tr><tr><td>12-08-14</td><td align="right"><tt>  511648</tt></td><td align="right"><tt>  280869</tt></td></tr><tr><td>13-08-14</td><td align="right">**<tt>  <span style="text-decoration: underline;">512521</span></tt>**</td><td align="right"><tt>  280918</tt></td></tr></tbody></table></td></tr></tbody></table>Modern routers didn’t all have this limitation – in fact [Cisco](https://supportforums.cisco.com/document/12202206/size-internet-global-routing-table-and-its-potential-side-effects) and [other people](http://v4escrow.net/ipv4-routing-table-expansion/) posted an advisory about the impending issues that the growing number of IPv4 routes were going to cause – but routers have typically been deploy-and-forget devices that are set up and then run with minimal interaction. Older devices were mostly configured with a default of 512k. When the number of advertised routes exceeded 512k, in the words of redditor [DiscoDave86](http://www.reddit.com/u/DiscoDave86),

> <span style="color: #000000;">“Upon further investigation it appears that the IPV4 public address space exceeded 512k routes, and some older routers have that as their limit due to memory constraints, consequently a whole load of routers became expensive doorstops”</span>

The fix is simple enough: reconfigure the routing table limits on your router, but it requires a reboot of the device and rebooting a core router is not a task undertaken lightly.

Many ISPs have been scrambling to reconfigure their hardware to make sure they aren’t stung by this again, but the effects have been far-reaching as a result.

##### Impact

Update: I’ve drawn some quick & dirty nodegraphs to illustrate what happens when routers reboot.

In this (very simplistic) illustration of the Internet, Node 1 is trying to connect to Node 7. The **bold** path is the path its network traffic takes across the ‘net.

![](https://ap-content.storage.googleapis.com/2014/08/Selection_048.png)

Everything is normal – traffic is routed according to the hop distance (fewest nodes to target). This isn’t always how it works in reality, but for the purposes of this example, it’ll do.![](https://ap-content.storage.googleapis.com/2014/08/Selection_046.png) Node 4’s administrator notices the problem, applies the fix and reboots the router, causing all routes that are using Node 4 to fail and have to be re-calculated.

![](https://ap-content.storage.googleapis.com/2014/08/Selection_051.png)Nodes 4 and 8 are offline pending a reboot, so the path from N1 to N7 is routed through N2 and N6. *Any addresses behind N4 and N8 are offline and become un-routable. It’s as though they no longer exist. *

![](https://ap-content.storage.googleapis.com/2014/08/Selection_051.png)N4 is back online but now has to re-create its routing table and only adds N1 and N7, so it can no longer route to N3 and N5

![](https://ap-content.storage.googleapis.com/2014/08/Selection_050.png)N8 is back online and starts to recreate its routing table, adding N1 and N4 as its available nodes.

![](https://ap-content.storage.googleapis.com/2014/08/Selection_051.png)After the nodes reboot, this is the final state of the network. As you can see, N4 and N8 have not got their original routes back, necessarily.

This is a very simplistic representation of what happens when you reboot a core router attached to the Internet, such as those that the likes of L3, AboveNet, TiNET, NTT operate. I haven’t included link costs in this diagram, either.

Last night, when many ISPs were doing this, entire blocks of addresses simply became un-routable. You didn’t get timeouts or dropped packets or lag. They just didn’t exist, for all the Internet was concerned.


