+++

author = "Kyrill"
categories = ["fullwidth", "Grok", "Internet", "Logstash", "Tech", "Tutorials"]
date = 2014-04-01T11:08:06Z
description = ""
draft = false
image = "https://ap-content.storage.googleapis.com/2014/04/Selection_024.png"
slug = "useful-logstash-grok-patterns"
tags = ["fullwidth", "Grok", "Internet", "Logstash", "Tech", "Tutorials"]
title = "Useful Logstash GROK patterns"
+++
In my [previous post](http://antisp.in/2014/03/getting-started-with-logstash/ "Getting started with Logstash"), I outlined how I manage the collection of logs across our infrastructure at a high level with Logstash and Elasticsearch. I also touched upon viewing and searching through the data with Kibana, a Javascript frontend.

In this post, I want to cover an important interim step if using the packages in the ElasticSearch repos is unfeasible or if you are running legacy servers that the repos don’t provide packages for.

In order to collect data from our remote machines, we’ll need a copy of *logstash-forwarder* which is a lightweight, stand-alone version of Logstash that only deals with the shipping of logs and comes with a package all of its own.
#### You’ll need:

- The GO language ([download](http://code.google.com/p/go/downloads))
- FPM (Fucking Package Manager)
- Git

You can install FPM and Git by running

sudo apt-get install rubygems git sudo gem install fpm

For Go, you’ll want the 32- or 64-bit generic Linux tarball. You can extract it wherever you like, but if you just want to get it working, you can extract it into your home directory and be done with it. You’ll also need to add some environment variables so that Go works properly. Bear in mind that these will only be around for as long as you don’t close your shell.

export PATH=$PATH:$HOME/go/bin export GOROOT=$HOME/go

#### Build your logstash-forwarder package

*Instructions taken from [https://github.com/elasticsearch/logstash-forwarder](https://github.com/elasticsearch/logstash-forwarder)*

git clone git://github.com/elasticsearch/logstash-forwarder.git cd logstash-forwarder go build

To package it up into a .deb package, you can run

make deb

While in the logstash-forwarder directory.

At this point, you’ll have a new, shiny logstash-forwarder_<version>_<arch>.deb file. Bear in mind that if you’re looking to build for a 64-bit architecture, to the best of my knowledge, you’ll need to build on a 64-bit machine.

You can deploy this to your client boxes. I’ll cover installing and setting up in the next post.

 


