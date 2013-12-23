---
layout: page
title: IPodDiscussion
---



This is a simple discussion on a few ideas. Feel free to vote or comment.

1. Apple supply us with an SDK for the iPod on the Mac side so that we can take advantage of it more easily (or make iPod.framework a real framework, not a private one).

2. Apple supply us with an SDK so that we can write software for the iPod to run on the iPod.

3. Apple supply us with more documentation on how to take advantage of services in  the operating system to further broaden the iPod's capabilities.

Discuss:

----

On #2, we might not need Apple's help for that. The iPod OS is not Apple-designed, they bought it from a third party. I visited their site once, but I can't recall the name. -- DustinVoss

Well, it certainly can be done without Apple's help. Just ask whoever got Linux running on iPods. (See http://ipodlinux.sourceforge.net/) -- AngelaBrett, who wishes she could use the new software on her old iPod

Apple just posted some hardware information at http://developer.apple.com/hardware/ipod/. -- DustinVoss

----

I believe the name of the company is PortalPlayer and the OS is called tango.  

-Jeremy

----

I thought the OS was something designed on top of the Pixo OS.  I don't know what else (cell phones) uses Pixo, but the interface is not *all* that much different - a menu structure basically - the rest is just code underneath to generate the menu structure.  I've been wonderif if it would be possible to disassemble the firmware itself (does the Nano and 5G Video iPods still use an ARM core?), and try to figure out how to write something for the native OS..  I'd love to be able to make something that would plug in under the Extras menu or Games menu.  A track editor for instane..  I could do that in Linux, but that would require a reboot, and for an iPod OS, I really would want something that is as totally memory resident as possible (I don't know if the uClinix port is), as the iPod has no venting for the HDD (just a really damn big heat sink!).

  Does anyone know what the iTunesControl application (??) on the iPod (it's hidden in one of the directories) does?  I tried deleting it, and it didn't affect anything it seemed - I put it back just in case.

  How does iTunes interact with the iPod - is the iPod seen as some kind of an external file system (that's not used by the Finder - the Finder simply sees it as another HDD), or is it all done through the iPod and iPodSync private frameworks?  And what is the difference between the two?  Why have two, unless Apple plans to open one of them up at some point.

  I'm wondering if you could write a kext to make something *other* than an iPod appear as one to iTunes - something like a CF or other card formatted with the Newton "file" system (yes, Newtons can use ATA cards now (http://www.kallisys.com/newton/ata/ ), and there is an MP3 player (http://40hz.org/MADNewton/ ).

- Jim

