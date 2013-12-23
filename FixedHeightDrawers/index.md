---
layout: page
title: FixedHeightDrawers
---



This one is from the Mac OS X list, from a posting by Douglas Davidson:
(I might put in some CocoaSampleCode later if I get the time)

Here's the procedure I was using to get a fixed-height drawer:

* don't try to set the drawer's minContentSize;
* in the parent window's delegate, implement windowDidResize: and in 
that method set the drawer's trailing offset based on the parent 
window's frame size (take the height and subtract some fixed constant);
* in the parent window's delegate, implement windowWillResize:toSize: to 
constrain the parent window's height so that there is sufficient room 
for the drawer.

-- JensBaumeister

