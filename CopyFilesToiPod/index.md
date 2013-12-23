---
layout: page
title: CopyFilesToiPod
---



I am interested in copying files to an iPod when it's NOT in disk mode. Any way to go about doing this? Using Apple's sample USBNotificationTool, I can get my app to recognize when an iPod is plugged in or disconnected, but that's about it. How do I copy something to a USB device?

----
I don't have an answer for you, I'm afraid, but I'm intrigued. What is it, specifically, about disk mode that you want to avoid? I ask because it strikes me that you could easily end up re-implementing an awful lot of functionality that is already provided by disk-mode just to make this work.

----
You have to mount the iPod first, then copy files to it like normal, then unmount. This is how the iPod.framework does it (which is what iTunes uses).

----
Even on the iPod touch?

----
The iPod touch doesn't support disk mode, AFAIK.

----
That's what I thought. Consequently, I need to figure out another way of copying files to it...

Here's a guy that wrote a kext to get his phone to show up as a USB device: http://magnus.nordlander.tk/technology/sonyericsson-m600i-usb-file-mode-enabler. Think the same thing would be possible?

----
I believe that the file system on the touch is encrypted, and not open just hidden as on earlier iPods. At the time of writing (Oct 11. 07) I have heard that the touch scheme has been cracked but so far no-one has written any utilities to exploit it to actually transfer files. More info here: http://www.toc2rta.com/?q=blog --GC

