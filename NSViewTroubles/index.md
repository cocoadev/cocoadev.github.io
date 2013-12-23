---
layout: page
title: NSViewTroubles
---

I have a problem with an NSView consisting of subviews contained in an NSMutableArray. The problem is related to distorts in the views occuring when performing mouse actions in one of the views. The other views gets darker and darker as apparently the views are redrawn repeatedly. How do I solve this highly annoying problem? The views display fine initially, but gets rather messy when I try to do something useful.

*Are you calling -setNeedsDisplay:YES or -display when views are moved, resized, etc.? You have to account for all of that stuff. You might need to describe your views with a little more detail as you haven't provided enough information to give you more than a guess. Depending on how you're drawing stuff, you might need to just flag the container view for display (see methods above).*

