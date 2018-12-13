+++
author = "Kyrill"
categories = ["fullwidth", "Internet", "Logstash", "Tech", "Tutorials"]
date = 2014-03-04T17:44:16Z
description = ""
draft = false
image = "https://ap-content.storage.googleapis.com/2014/03/Selection_017.png"
slug = "getting-started-with-logstash"
tags = ["fullwidth", "Internet", "Logstash", "Tech", "Tutorials"]
title = "Getting started with Logstash"

+++


### Hello,

I’m a sysadmin. I manage servers for a living. About a hundred of them, at last count. One of the biggest challenges in my job is keeping tabs on what’s going on inside my infrastructure.

- Was the last email sent to dave.mcUserface@example.org delivered?
- What’s the busiest nginx host in the network?
- Which vhost gets the most traffic?
- What was the average load across the entire infrastructure at 04:53, three days ago?
- Where did that spike of HTTP 502 errors at 21:24 come from?

More importantly, maybe, how do I present that information in a concise and digestible format? And how do I quickly find the information I want?

When you have 2-3 machines to manage, it’s feasible to look through the relevant log line by line. But then, how do you spot a trend among log entries that often differ by a few characters?

**Across forty web servers. **

This, however, is not a theoretical discourse into how to do this. I use [Logstash](https://www.logstash.net/) combined [with Collectd](https://logstash.net/docs/1.3.3/inputs/collectd). There are other solutions out there – Splunk comes to mind, if you hate your money so much you want to pay exorbitant license fees.

![](https://ap-content.storage.googleapis.com/2014/03/Selection_017.png)

 

As you can see, the data that’s collected falls broadly into two categories:

1. System-level metrics (load, free memory, disk, interface, etc)
2. Application/service-level metrics (web server logs/syslog/application logs/mail logs)

While it’s perfectly possible to do everything with *collectd*, I wouldn’t recommend it. More on this in the next post.

### Getting Started

I run all this on Debian, so all instructions will be for that platform.

Firstly, you’ll want to install Logstash on a server. Preferably a dedicated box. Depending on how many devices you are harvesting logs from, you’ll need at least 4GB memory for your Logstash box, but the more RAM you give it, the better.

In days gone by, you had to install Logstash manually by downloading the Java *jar *and then getting some sort of initscript set up.

These days, all you need to do is add the Logstash and Elasticsearch repos to your sources.list (as root), update and install.

echo "deb https://packages.elasticsearch.org/logstash/1.3/debian stable main" > /etc/apt/sources.list.d/logstash.list echo "deb https://packages.elasticsearch.org/elasticsearch/1.0/debian stable main" > /etc/apt/sources.list.d/elasticsearch.list wget -O - https://packages.elasticsearch.org/GPG-KEY-elasticsearch | apt-key add - apt-get update apt-get install logstash elasticsearch

***Update ***

***Alternatively, go here: [https://www.elasticsearch.org/overview/elkdownloads/](https://www.elasticsearch.org/overview/elkdownloads/) and download the individual components as .deb or .rpm packages to taste***

You’ll also need to install redis, available from the Debian repos

apt-get install redis-server

Redis is a message broker that handles incoming events and passes them to Logstash to analyse. You shouldn’t need to configure Redis at all.

[Click here for the basic configs for your elasticsearch and logstash services  
](https://gist.github.com/poolski/9351107)(if you have problems, please post a note on Github or here)

### Rock and/or roll

Once that’s rigged up, you should be able to start all the services:

sudo /etc/init.d/redis-server start sudo /etc/init.d/elasticsearch start sudo /etc/init.d/logstash start

Check they’re all running:

sudo ps aux|grep elasticsearch sudo ps aux|grep logstash sudo ps aux|grep redis

You should get something like this

root 23369 40.3 7.0 2876996 1190452 ? Sl 06:25 266:25 /usr/bin/java -Xmx2g -Djava.io.tmpdir=/var/tmp/logstash/ -jar /opt/logstash/logstash.jar agent -f /etc/logstash/conf.d --log /var/log/logstash/logstash.log -w 8

At this point, Logstash should be tailing your syslog and feeding the events it receives into Elasticsearch. But how do we get from logs to pretty pictures like this?

![](https://www.elasticsearch.org/overview/kibana/).

You can install it wherever you like – it’s a JavaScript frontend to Elasticsearch and you can run it on whatever web server you like – you don’t need PHP or any other fancy business.

Download it, unpack it to a directory on your web server (this can be nginx/apache running on your new Logstash box or another server), e.g. */var/www/kibana* and create your vHost as required/where appropriate (outside the scope of this article).

If you’re installing on a server other than your Logstash box, you’ll need to make sure Kibana can connect to your Elasticsearch instance. Edit **config.js** in your Kibana install folder and change the line that says

elasticsearch: "https://"+window.location.hostname+":9200",

to

elasticsearch: "https://yourserver.yourdomain:9200",

You can also point it to an IP. As long as port 9200 is accessible, it should work.

In the next post, I’ll explain how to do more advanced collection using the lightweight *logstash-forwarder* log shipper and how to collect system metrics using *collectd.*

 


