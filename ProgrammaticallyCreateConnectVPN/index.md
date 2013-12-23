---
layout: page
title: ProgrammaticallyCreateConnectVPN
---

 

The title says it all. I need to programmatically create / connect VPN connections without running Internet Connect. I have looked at the MoreSCF modules - in fact I am using some of that to create/connect PPP links. However, there is no documentation or sample code for VPN configuration or link management. Anyone have any info on what's required ?

Thanks - Alec

----

Have you considered AppleScript?

*Or type "man pppd" in the terminal...*

----

>  Have you considered AppleScript?

Yes, the problem is that AppleScript will run Internet Connect, something I want to avoid (if possible).

>  *Or type "man pppd" in the terminal...*

I have the pppd sources (available on darwin) - this will get me connected, but not configured... ):

----

create an xml-plist file with the extension .internetconnect

to understand the format, manually create an entry and then export it.

----

Hi you guys:

I have a similar concern, I would like to utilze pppd to have my Darwin (v.8) clients connect to my VPN server (PPTP protocl) and was wondering what is the correct syntax with the following features: VPN PPTP, Chap, 128-bit mppe, and remote user id & pass. It is noteworthy to say that the clients connect to the server through broadband connection and not dial up.

Appreciate any help on this matter.

Nawaf

----

What type of VPN is the original question referring to?  IPSec, PPTP, PPTP over IPSec, or some other?  (Sorry Nawaf,  I don't use PPTP, so I can't help there).

If you are trying to establish an IPSec VPN, Mac OS X uses a tool called "racoon" to set up the IPSec session.    I don't know about programmatically doing that.. I think most tools just do the config file setup, then exec racoon.    Note that racoon needs admin privileges to function.

