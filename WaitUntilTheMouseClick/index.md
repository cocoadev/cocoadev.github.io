---
layout: page
title: WaitUntilTheMouseClick
---

I think people who ever programmed with General/HyperCard will recognize the title of this page. In General/HyperCard, you could type this on a line :
    
wait until the mouseclick
do something
[...]


And it told the computer to well... the line explains it all. I want to do that in Cocoa, with a General/NSView, but I have no idea how to implement it. Any hint?

-- Trax

So, as opposed to using the regular event-driven -mouseUp: (analogous to General/HyperCard's 'on mouseUp' or whatever handler it was), you want to start some action or other, require the user to click, and then continue on afterwards?

Add a BOOL flag to your view class, something like needsClick. When you start the 'wait until the mouseclick' code, set needsClick to YES. Then, in your -mouseUp: implementation, check to see if(needsClick), and if so you can 'do something,' otherwise just return;.

Sound good?

-- General/RobRix
