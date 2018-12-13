+++
author = "Kyrill"
categories = ["fullwidth", "Tech", "tutorial"]
date = 2014-11-10T17:20:04Z
description = ""
draft = false
image = "https://ap-content.storage.googleapis.com/2014/11/huge-slack-logo-on-white.png"
slug = "integrate-gitlab-slack-properly"
tags = ["fullwidth", "Tech", "tutorial"]
title = "Integrate Gitlab with Slack properly"

+++


In case you haven’t heard of Gitlab yet, it’s an open-source alternative to GitHub’s own Github-FI software package. Github-FI is Github’s self-hosted version of their SaaS offering. Basically, you can get your own GitHub inside your network.

However, if you *don’t* have several thousand dollars to blow, you can use GitLab-CE (Community Edition) instead. It offers a large subset of features that GitHub has with many integrations with external services. What’s even better is that many services are now starting to develop [GitLab](https://about.gitlab.com) integration, but the number is still low.

One of the most useful services for DevOps/Project teams that I’ve come across is Slack. [Slack](https://slack.com) allows you to communicate and collaborate in realtime, regardless of OS, location, etc. It’s slick, well-designed and integrates with many other web services. ![](https://ap-content.storage.googleapis.com/2014/11/app-200x200.png)

GitLab has had Slack integration since GitLab 6.4, but like its HipChat integration the functionality is limited to pushing notifications of `git push` events to chat. This generates a lot of noise in busy projects while having minimal *signal*.

Unfortunately, Slack’s list of API-based (as opposed to Webhook-based) integrations doesn’t include Gitlab at present.

#### The good news

Slack have released Hammock, a PHP “Integrations Framework” for you to roll your own integrations, routed through an internal gateway service which can send fully-formatted messages to Slack.

I’m going to assume you know how to install a basic PHP environment with either *nginx* or Apache with PHP.

**Hammock requires *curl* so ensure it and *php5-curl *are installed. **You’ll probably also need *git.*

Ready?

[Go here](https://github.com/tinyspeck/hammock) and run through the installation instructions. Remember to install *curl* and *php5-curl*.

Once that’s done and you’ve authenticated your Hammock instance against your Slack account, you’ll want to grab [my Gitlab Issues plugin](https://github.com/poolski/hammock-gitlab-issues) and copy it into your $hammockdir/plugins folder. You can get it [here](https://github.com/poolski/hammock-gitlab-issues).

Once that’s done, you’ll be able to create webhook endpoints for Gitlab to send events to. All that remains is to configure your projects to send issues events to Hammock which will forward them to a channel of your choice.

[  
](https://ap-content.storage.googleapis.com/2014/11/huge-slack-logo-on-white.png) ![](https://ap-content.storage.googleapis.com/2014/11/Selection_069.png)


