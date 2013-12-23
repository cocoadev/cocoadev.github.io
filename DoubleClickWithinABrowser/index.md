---
layout: page
title: DoubleClickWithinABrowser
---



I am wondering if it is possible to use double clicking within a NSBrowser. I am using Applescript studio at the moment, but I do have some background in cocoa and objective-c. Does anyone know if this is possible or can happen with applescript studio? If not, what would be a good way around creating a fast browser that can be used to move through files (not including the finder, I want to be able to use the files for custom applescript actions). 

Thanks,
Kevin W

----

I don't know anything about AppleScript Studio, but NSBrowser has a     - (void)setDoubleAction:(SEL)aSelector that does what you want.

----

Yea, I think I have realized that I will need to use cocoa to accomplish what I need to. It would be easier in applescript, but cocoa gives me more flexibility and control over my options.

