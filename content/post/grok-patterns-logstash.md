+++
author = "Kyrill"
categories = ["fullwidth", "Logstash", "SysAdmin", "Tech", "Tutorials"]
date = 2014-09-19T15:34:10Z
description = ""
draft = false
image = "https://ap-content.storage.googleapis.com/2014/09/keep-calm-and-grok-on.png"
slug = "grok-patterns-logstash"
tags = ["fullwidth", "Logstash", "SysAdmin", "Tech", "Tutorials"]
title = "Grok patterns in Logstash"

+++


After posting a set of [postfix and sendmail Grok patterns](https://antisp.in/2014/04/useful-logstash-grok-patterns/ "Useful Logstash GROK patterns"), I’ve had a request to explain how to implement Grok patterns in Logstash. So, here we go.

I’m going to make some assumptions about your installation of Logstash – on a typical Debian install, assuming you installed from the Elasticsearch PPA (hint: you should be doing this), your Logstash configs will be in /etc/logstash/conf.d/ or something of the sort.

|/etc/ |-logstash/ |--conf.d/ |---inputs.conf |---filters.conf |---outputs.conf |--patterns/ |---sendmail.grok |---postfix.grok

As you can see, the Grok patterns go in /etc/logstash/patterns/ in my case. You can realistically put them anywhere you like, providing the logstash process can access the location.

Anyway, if you want to make use of any Grok pattern (assuming it’s valid), it’s a fairly straightforward operation:

filter { grok { patterns_dir => "/etc/logstash/patterns" pattern => "%{SENDMAIL}" named_captures_only => true } }

The named_captures_only will tell Grok to only capture fields that are named in your Grok pattern and discard the cruft – useful when you want to extract a few key metrics from an event.

If you look at the [Sendmail grok file I posted here](https://antisp.in/2014/04/useful-logstash-grok-patterns/ "Useful Logstash GROK patterns"), you can see how it’s being referenced above. This method is deprecated, but it still works for me, so that’s what I’m sticking with.

You can get creative and only invoke Grok for certain types of events by using an

if [type] == "something" { grok { patterns_dir => "/etc/logstash/patterns" pattern => "%{SOMEPATTERN}" named_captures_only => true } }

Hope this helped!


