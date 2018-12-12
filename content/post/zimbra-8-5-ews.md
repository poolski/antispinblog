+++
author = "Kyrill"
categories = ["fullwidth", "Internet", "Servers", "SysAdmin", "Tech", "Ubuntu", "Zimbra"]
date = 2014-09-18T15:37:26Z
description = ""
draft = false
image = "https://ap-content.storage.googleapis.com/2014/09/166088.jpg"
slug = "zimbra-8-5-ews"
tags = ["fullwidth", "Internet", "Servers", "SysAdmin", "Tech", "Ubuntu", "Zimbra"]
title = "Zimbra 8.5 EWS"

+++


Zimbra have recently released Zimbra Collaboration Suite 8.5 (JudasPriest).

One of the most-hyped features of 8.5 is native support for Exchange Web Services (EWS), meaning Macs and Outlook clients can natively connect to EWS instead of using the Zimbra Connector or IMAP.

After laying the groundwork for doing the upgrade – moving data around, system updates, installing the LTS Enablement pack from Canonical on Ubuntu 12.04 to upgrade the kernel among other things, we were ready for the upgrade to 8.5.

#### Watch out for Puppet

<span style="color: #800000;">If your Zimbra box is managed by Puppet and if Puppet manages your */etc/sudoers* file, you’ll want to stop the agent before running the upgrade. The reason for this is that 8.5 has additional commands that the Zimbra user needs to be able to run with elevated privileges. It’ll update your sudoers file as part of the upgrade, but there’s a danger that Puppet will roll it back which can seriously cock up your installation.</span>

#### EWS and Touch Client

Do not work out of the box.

It turns out that if your license was purchased before the 1st of September 2014, you are not eligible to use the EWS functionality, despite enabling it in the CoS settings in Zimbra.

Needless to say, I am not currently best-pleased with Zimbra for effectively locking out users who have purchased perpetual licenses and a support pack which is supposed to provide access to upgrades. According to them, however, it seems that since EWS was not available as a feature at the time of purchase of the license, you need to pay extra to get it.

Anyone else having this problem with Zimbra? Anyone else been bitten by Zimbra’s sneaky pricing tactics?

###### Update

Zimbra have come back to us and have said the following:

> <span style="color: #993300;">*Outlook for Mac is not an upgrade feature but an add-on.  Because there is a royalty associated with it, Zimbra chose to not increase prices for the product editions for everyone to accommodate that expense but rather to simply allow those companies who needed it, the ability to purchase it separately.  *</span>

Which is interesting, given that it’s described as a “New Feature” in their 8.5.0 [upgrade guide](http://files.zimbra.com/website/docs/8.5/ZCS_850_NE_ReleaseNotes_UpgradeInst.pdf)

###### Update 2

Twitter user [@jorgedlcruz](https://twitter.com/jorgedlcruz) has done a bit of asking around and has let me know the score.  
 Seems that EWS *is* an optional feature and the feature matrix reflects this. But it’s only available to resellers and internally.

> [@poolski](https://twitter.com/poolski) I saw now in the Official PDF for BSP, EWS is optional, an addon. Take a look if you can to the new Matrix // [@Zimbra](https://twitter.com/Zimbra)
> 
> — Jorge de la Cruz (@jorgedlcruz) [September 19, 2014](https://twitter.com/jorgedlcruz/status/512905018769682432)

<script async="" charset="utf-8" src="//platform.twitter.com/widgets.js"></script>

> [@poolski](https://twitter.com/poolski) [@Zimbra](https://twitter.com/Zimbra) You need to be BSP, or Partner, and then do login here – [https://t.co/Bx7XXWrAoT](https://t.co/Bx7XXWrAoT) Is not public yet. I will ask for publish
> 
> — Jorge de la Cruz (@jorgedlcruz) [September 19, 2014](https://twitter.com/jorgedlcruz/status/512905920649887744)

<script async="" charset="utf-8" src="//platform.twitter.com/widgets.js"></script>


