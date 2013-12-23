---
layout: page
title: HowToMergeBezierPaths
---

I'm creating an application in which the user can select portions of a view. The selection is stored as an NSBezierPath and is indicated to the user using [path stroke]; My problem comes when I add to the selection: by holding the shift key, the user can define a new selection to add to the existing selection. I combine the two using: 
    
[path appendBezierPath:path2];

The problem with this is that when I stroke the new selection, both paths get stroked completely. The desired behavior is to draw an outline of only the merged paths, not of each path individually. Is there a way to do this?

Thanks,
MattBall

----

What you're thinking of is doing a union of two paths, which isn't available as a simple method on NSBezierPath and I'm not sure about anything in CoreImage. You'll most likely have to do the union yourself, calculating if and where the two paths intersect.

----

There is no easy way to do this within the Cocoa API at present. There should be, in my opinion. I just added a section at AppKitMostWanted so maybe you could add your vote here, for what it's worth. There are actually a number of things Quartz cannot do that were trivial with QuickDraw regions. See also NSBezierPathcombinatorics.

