---
layout: page
title: InternetEnabledDiskImages
---

Internet-enabled disk images are described at http://developer.apple.com/documentation/DeveloperTools/Conceptual/SoftwareDistribution/Concepts/sd_disk_images.html

----

Apple highly recommends that you use DMG<nowiki/>s or ZIP<nowiki/>s for distributing your apps, since Stuffit is no longer distributed with Mac OS X "out of the box", but DMG<nowiki/>s and ZIP<nowiki/>s will work on a fresh install of the OS, with little to no user interaction, and they provide most of the features you need - background images, license agreement, etc.

----

Apple documentation on embedding SLA (SoftwareLicenseAgreement) in .dmg is out of date, since ResEdit is deprecated. 

The following steps will produce the same result :


Create a Source.dmg and put the files in it. Unmount.
    hdiutil convert -format UDCO -o Release.dmg Source.dmg
hdiutil unflatten Release.dmg
Edit SLA.r file to your needs
    /Developer/Tools/Rez /Developer/Headers/FlatCarbon/*.r SLA.r -a -o Release.dmg
hdiutil flatten Release.dmg
hdiutil internet-enable -yes Release.dmg


To get a SLA.r file :

Get SLA<nowiki/>Resources in the SLA SDK on Apple Developer Connection :  ftp://ftp.apple.com/developer/Development_Kits/SLAs_for_UDIFs_1.0.dmg
    Developper/Tools/Derez SLAResources > SLA.r


or get a more readable one, with a MIT opensource license, here : http://simile.mit.edu/repository/welkin/trunk/misc/SLA.r

