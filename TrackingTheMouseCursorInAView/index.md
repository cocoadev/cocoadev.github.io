---
layout: page
title: TrackingTheMouseCursorInAView
---

Hi,
I was wondering if anyone knew how one could track the location of the mouser in a View. I have a subclass of General/NSOpenGLView and wish to get the location of the mouse whenever the mouse movers around it(as in many drawing programs). Can anyone suggest some pointers? I've seen trackingrectangles but i don't thats helping me.
Thanks
sapsi
----
- mouseDown:
- mouseDragged:
- mouseUp:
- mouseMoved:

http://developer.apple.com/documentation/General/GraphicsImaging/Conceptual/General/OpenGL-General/MacProgGuide/5OpenGLCocoaProg/chapter_5_section_4.html

http://developer.apple.com/documentation/Cocoa/Reference/General/ApplicationKit/ObjC_classic/Classes/General/NSResponder.html

http://developer.apple.com/cgi-bin/search.pl?q=mouseDown&num=10&site=default_collection

See the Sketch example application on your hard drive.
----
Check out the addition of General/NSTrackingArea in 10.5
----
Use **setAcceptsMouseMovedEvents:YES** on window's openGLView. If openGLView is first responder, then it receives mouseMoved events.
