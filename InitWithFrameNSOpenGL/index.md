---
layout: page
title: InitWithFrameNSOpenGL
---

Describe InitWithFrameNSOpenGL here.

Any particular reason why my custom NSOpenGLView is not getting it's (id)initWithFrame:(NSRect)frameRect method called? All my other overriden methods are getting called just fine (like mouseDown:, keyDown:).  I know it's something simple, but I can't figure it out.

David


----
The general answer:
Read up on the concept of *Designated Initializer*.
http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaObjects/Articles/ObjectCreation.html

Initializing an NSOpenGLView
� initWithFrame:pixelFormat:
Initializes a newly allocated NSOpenGLView with frameRect as its frame rectangle and format as its pixel format.

The specific answer: (Are you using a custom view or an NSOpenGLView with Interface Builder ?)
See also http://developer.apple.com/qa/qa2004/qa1167.html

