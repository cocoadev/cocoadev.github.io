---
layout: page
title: ShadowedTextNSView
---

Hi,

I've been using cocoa now for 6 months, but have only just come across the need for drawing text in an NSView which is shadowed. Now i know how to draw the text in the first place, i just need to be able to draw the shadow.What do i need to use to get this working?

Basically i'm trying to do text similar to that which the desktop icons use.

Thanks
TomHancocks

----

Like any other attribute, shadowing is an attribute that you add to the NSAttributedString (or that you add to the attributes dictionary for a plain NSString). You can see a full list of attributes in the docs, or by opening AppKit/NSAttributedString.h in Xcode. The attribute you want here is NSShadowAttributeName.

For drawing shadows around arbitrary drawings, not just text, see the NSShadow class.

----

Thanks a lot, I was looking at NSShadow but not getting anywhere.

----

To use NSShadow, you'd create the shadow object and then set it, like a color, by doing     [shadow set] before doing the actual drawing 

----

If you care about 10.2 compatibility, you can use NSClassFromString to check whether the NSShadow class exists before using it as described on JaguarOrPanther

----
I was searching around for a version of NSTextField that would draw a shadow.  Didn't find one (didn't look hard either), here's one that does the trick - its a derivation of NSTextField and overrides the drawing code such that a shadow gets set up before the standard drawRect: is invoked, thus you get to keep all the lovely IB goodness etc.  

http://idisk.mac.com/john_clayton//Public/permanent/ANTextField.zip

