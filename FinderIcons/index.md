---
layout: page
title: FinderIcons
---

How do I get the 48x48 icon from a file like finder does? The times I have tried, I have used:

    
NSImage *icon = [[NSWorkspace sharedWorkspace] iconForFile:path];
NSLog (@"%@", icon);	
[icon setSize:NSMakeSize(48, 48)];
[fileEntry setObject:icon forKey:@"icon"];


The display from the NSLog shows:

    
NSImage 0x1638c100 Size={32, 32} Reps=(
    NSBitmapImageRep 0x1638d600 Size={128, 128} ColorSpace=NSDeviceRGBColorSpace BPS=8 BPP=32 Pixels=128x128 Alpha=YES Planar=NO Format=0, 
    NSBitmapImageRep 0x1638c5f0 Size={32, 32} ColorSpace=NSDeviceRGBColorSpace BPS=8 BPP=32 Pixels=32x32 Alpha=YES Planar=NO Format=0, 
    NSBitmapImageRep 0x1638c700 Size={16, 16} ColorSpace=NSDeviceRGBColorSpace BPS=8 BPP=32 Pixels=16x16 Alpha=YES Planar=NO Format=0
)


I end up with a nasty resized imaged which has nothing to do with the quality one that Finder shows. Any ideas?
----
Not all Finder icons *have* a 48x48 icon representation, in which case the 128x128 one is scaled down. Unfortunately, it does seem that Finder's icon-scaling code is smarter than NSImage's     setSize:, for whatever reason. Your approach is correct, but the size you picked is somewhat unfortunate. 32x32 is a more common size (as your log shows), and 64x64 will scale much more nicely from 128x128. Or perhaps someone else could suggest a way to use one of the graphics frameworks to do something about this (someone better with graphics than I am). --JediKnil

----

Simply draw the 128x128 icon in a 48x48 rect. You*ll get a beautifully resized image.

Have you tried adding [icon setDataRetained:YES]; before you set the size?

----

Try adding this line to your view's init method:

    
[[NSGraphicsContext currentContext] setImageInterpolation:NSImageInterpolationHigh];


----

That won't help. There's no way to know the current graphics context in an -init method unless you've locked focus on something first. Instead, that line needs to go in the -drawRect: method of the view before the icon is drawn.

----

I'm more than willing to admit it might be bad code, or even a bug, but it ABSOLUTELY does work in an init method. (I have shipping code that uses it...) Would love further details tho. :-)

----

If it works, it works by coincidence. Probably because your window's graphics context is current when you're initialized or something along those lines. It definitely shouldn't be relied on, though. Treat graphics contexts as something ephemeral and set them up every time you draw, and you'll get more reliable behavior.

----

I probably should have mentioned that I am displaying the icon in an NSTableView, using my own image cell class. Within the drawWithFrame method, I do set the interpolation to high, and I paint the icon using the NSCompositeSourceOver operation. Still, the iCal icon, for example, looks very different from the one Finder produces:

http://www.purplevalor.com/iCalIcon.jpg

Maybe OSX keeps an internal icon storage for its standard applications?

----

So what happens if you change [icon setSize:NSMakeSize(48, 48)] to [icon setSize:NSMakeSize(128, 128)]; then draw the icon in a 48x48 rect?  Try it.

----

OP: I'm bored and need to get back into Cocoa, so here's some code. It'll draw all icons in /Applications, with NSImageInterpolationLow applied to the left hand column, and then re-draw them with NSImageInterpolationHigh applied to the right hand column. It's not a tableView, just a customView, but uses prototype cells, a dataSource, and you can live resize the icons.

Check it out and let me know. If you need further help, I can hop in the cocoadev AIM room or something. (ps. hacked together in a few hours, be gentle.. hope it helps. ;-)

http://pagesix.net/download/PrettyIcons.zip

EDIT: I've updated the sample code with an NSTextField showing the size of the icons. Also, the point of this whole app was to show that I don't think you're getting the NSImageInterpolationHigh value applied correctly. Basically, you should see something like follows if you were. You'll notice my iCal image on the left and your iCal on the right are identical. Mine is with NSImageInterpolationLow intentionally applied, whereas yours is I suspect, accidental (ie, the default behavior).

http://pagesix.net/misc/example.png

----

You are absolutely right. For some reason it was not using high interpolation. Now that it is, it works perfectly well. 

Thank you all for all your help!

