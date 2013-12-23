---
layout: page
title: OSImages
---

I'm trying to find the image, that you see when you insert a blank cd next to the disk in the Panther Sidebar, where would that be located?

----

I did this:
    
find /System/Library/PrivateFrameworks -name **.tiff' -or -name **.icns' | grep -i cd

And out comes:     /System/Library/PrivateFrameworks/Installation.framework/Versions/A/Resources/MultiCDInstaller.app/Contents/Resources/drive-CD.tiff --  is that the one you are looking for?

----

I'm talking about the burn icon, sorry about that.

----

you can use IconServices to get the icon. use GetIconRef(kOnSystemDisk, kSystemIconsCreator, kBurningIcon, &myIconRef), then use PlotIconRefInContext to draw into a CGBitmapContext, then use NSBitmapImageRep and NSImage as desired.

if you want an example of this:

http://boredzo.fourx.org/icongrabber/ [forthcoming]
http://boredzo.fourx.org/icongrabber/IconGrabber-1.0-src.sitx (Cocoa Objective-C source)
http://boredzo.fourx.org/icongrabber/IconGrabber-1.0.dmg (build)

*--boredzo*

