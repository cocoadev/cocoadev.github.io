---
layout: page
title: DetermineAColorsColorSpace
---


How would I determine whether an NSColor object is either an RGB or CMYK color?  My thought was to query a component and if it returned non nil then it is in that color space.  That doesn't seem to work, though, as it raises an exception if the component isn't defined for that color.

Thanks

----

[http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSColor.html#//apple_ref/doc/uid/20000353/BCICDDJG]

    - (NSString *)colorSpaceName

Returns the name of the receiver�s color space. This method should be implemented in subclasses of NSColor.

