+++
author = "Kyrill"
categories = ["DIY", "Documentation", "fullwidth", "Internet", "Tech", "Tutorials"]
date = 2012-06-18T09:47:16Z
description = ""
draft = false
image = "https://ap-content.storage.googleapis.com/2012/06/Screenshot-from-2012-06-18-103518.png"
slug = "piwik-varnish-cache"
tags = ["DIY", "Documentation", "fullwidth", "Internet", "Tech", "Tutorials"]
title = "Piwik with Varnish Cache"

+++


A couple of weeks ago, I installed the Varnish Cache daemon on the server this blog runs on to see if it’d speed it up any. The answer is, emphatically, yes. Images and pages load in seconds, as they’re being served from the static cache. The number of hits to Apache has decreased dramatically, which is fantastic!

The tradeoff was that Piwik, my analytics package that produces pretty graphs and charts like these…

![](https://ap-content.storage.googleapis.com/2012/06/Screenshot-from-2012-06-18-103715.png)
![](https://ap-content.storage.googleapis.com/2012/06/Screenshot-from-2012-06-18-103518.png)

… doesn’t play nice with a reverse proxy server, like Varnish. It complains violently when you try to log in because of the headers that Varnish sets aren’t what Piwik expects.

I spent about a week faffing about with Varnish configs trying to have Varnish totally ignore any requests for Piwik, but it still tinkered with the headers, if only to say, “I’m ignoring this!!!”.

After all that, I had a facepalm moment during which I realised it was much simpler.

**Run Piwik on a different port!**

- Varnish listens for connections on port 80 (http)
- Apache listens on port 81 for any requests that Varnish passes through to it on all vhosts.
- Apache *also* listens on 85 for Piwik’s vhost!

Thus, any requests going to Piwik are served by Apache and Varnish isn’t involved at all.

God, I love it when things are simple.


