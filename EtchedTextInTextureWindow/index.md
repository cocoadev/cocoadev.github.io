---
layout: page
title: EtchedTextInTextureWindow
---




I was wondering if anyone could point me in the right direction to producing text with the "Etched" look on a textured window as found in Safari, iTunes, iCal, etc... I have looked around the Cocoa text system documentation, but nothing stood out. I know that there are people who frequent this WikiWiki who have achieved this affect in their applications, and I would appreciate it if someone could help out.


Half hour later... UPDATE: Ok, I just figured out the way of doing it in IB using to NSTextField's, but is there a more efficient way of doing it? Actually it's more so that it needs to be a programmatic way of doing it, because I will be drawing the text into an NSView subclass, and I dont want to have to make multiple drawing passes over the same area when there may be a way to do it all at once. 

Lance

----

You could look at the source for AMRollOverButton in ObjectLibrary. For a cheap effect you could use NSShadow.

----

Draw the text white, move up a point, draw dark grey.

----

I know that this will work, but someone removed some of my orginal post which I have restored,  in which i explain that I am drawing this into a view, and dont want to have to make two drawing passes, painting over the same area twice which isn't very effient. Hence I am looking for a better way to do it.

Lance

*I doubt anyone intentionally removed your post - it was likely just an edit collision.*

----

Use NSShadow with a white shadow, offset to the top left

