---
layout: page
title: IPodNotification
---



I have a rather involving question. 

**Q:** First up, I know how to find an iPod that is mounted on the computer and use it. I can easily run a timer to check every 5s or so to see if an iPod is mounted for my app to use it, but is there any system wide notification that I can use and how would I access it?

**A:** Register for NSWorkspaceDidMountNotification and check to see if it was an iPod that was mounted?

**Q:** Second, iTunes manages to detect that there is an iPod attached when you first start it even if the iPod is not mounted. How does it do this and how does it mount it?


**A:** Probably using iPod.framework (see below)

**Q:** Thirdly, and finally, how does iSync and iTunes know to start when an iPod is connected? Is there any way my application can get similar benefits?


**A:** I think there is a private Apple framework (iPod.framework) that could do this, but it's not supported (and may break in future versions of Mac OS X). 

The other easy method is to listen for updates to the file system at /Volumes and find the iPod.

----

Could someone post some code explaining the use of the private iPod.framework? Or just private frameworks in general?

----

You may want to check into the DiskArbitration framework.  There are headers in /System/Library/Private Frameworks, and the source is in Darwin.  You can get notified when a new drive is mounted, and there may be a way to tell if this new drive is an ipoodle or not.

----

Easier...NSWorkspace has notifications for new mounted drives. Check if its an iPod by looking for iPod_Control.

----

hmm... what if something "slow" is mounted, like a tape archiver, a CD-ROM or similar, wouldn't it be to prefer, if one didn't have to search for files on the mounted device?

----

Oh I can search for files, but someone could copy the files to a disk image, just as I have done to test parts of my app. But with the framework, it may keep me from having to dig into SysInfo to get the iPods OS version.

