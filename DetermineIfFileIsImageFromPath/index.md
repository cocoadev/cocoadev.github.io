---
layout: page
title: DetermineIfFileIsImageFromPath
---

I am having difficulties my first real Cocoa program.  

In one function I am tryign to determine all the images in a  folder so thta I can copy them into a ScreenSaver.  I receive the path to the folder from an NSOpenPanel.  My idea was to get the array of files at the path then check each for their extensions.  This would be quite difficult cause I don't even know all the possible image types.  

My question to you is: What is the easiest way to check if a file is an image just from its path?

----

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSImage.html#imageFileTypes

You could try checking against     [NSImage imageFileTypes], I suppose. Or even just try init'ing a new NSImage with it, and if it fails, you'll know it's not gonna work :)

-- RobRix


----

Like Rob said, you can use imageFileTypes to see what types that NSImage will consume.  You can use that to test your files against.  My silly little borksort program at http://borkware.com/products/borksort/ walks a directory tree and brings in the ones that are images. ++MarkDalrymple

----

The sample code at this link demonstrates scanning a folder of your choice (which you can choose from the "Preferences..." menu item) for pictures.

http://developer.apple.com/samplecode/Sample_Code/Cocoa/DeskPictAppDockMenu.htm

