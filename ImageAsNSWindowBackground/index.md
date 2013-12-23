---
layout: page
title: ImageAsNSWindowBackground
---



It looks like a simple problem, but I can't solve it. I need to set an image (a gradient) as window background. The problem is that in NSWindow method     setBackgroundColor: the image is tiled across the window. And when I try to draw image in window's content view     drawRect:, then the image is drawn everywhere except the toolbar.

----

Instead of drawing it in the window itself, either add a view at the back of the window and draw it in there or use the content view of the window. --LoganCollins

----

The Title Bar and Toolbar are seperate views, see:

http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CoreAppArchitecture/chapter_7_section_4.html#//apple_ref/doc/uid/TP40002974-CH8-DontLinkElementID_122

----

Have a look at http://mattgemmell.com/files/source/tuneswindow.tgz on Matt Gemmell's site.
Make sure you read the license: http://mattgemmell.com/source/

----

The trick is basically to create a custom NSView subclass, then set is as the window's content view. You'll do this in Interface Builder in the outline view.

Here's a tutorial on Cocoa window backgrounds:

[http://www.mere-mortal-software.com/blog/details.php?d=2007-01-08]

