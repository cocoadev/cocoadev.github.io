---
layout: page
title: CanTheDockIconBeADragDestination
---

I think what I'm trying to do is simple, but I suspect it's not possible in this carnation of OS X.  I am trying to drag an image off a Safari page, over a (running or otherwise) dock icon, and release it, and have the app respond appropriately.  I crafted a test app, set an object as the app's delegate, registered the main window for all drag types, and NSLog all the notifications and respondsToSelector calls, but nothing happened in the app when dragging an image over the dock app.  That, and the fact that no other apps on my system seem to respond to a dragged image leads me to thing that the only thing that will tell an app to respond is the Finder itself.  Unless there's some hidden message or something I'm overlooking, which is why I'm bringing it up here. Has anyone had any experience with this?

----

I don't think this is possible, at least not without some serious hacking of Launch Services, the pasteboard system, the Dock, and probably more. The Dock doesn't map pasteboard types to file types. You could probably do what you want with a NSMenuExtra/NSStatusItem though, or just have a separate window for your app. Or see if you can somehow respond to drags to a screen edge. 

----

Oooh -- screen edge.  I forgot!  And look, I have an unused corner, too!  Thanks, I will look into that.

