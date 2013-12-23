---
layout: page
title: NSOpenGLContext
---



A wrapper for OpenGL in Cocoa. NSOpenGLContext lets you display OpenGL graphics in a view, or full screen. Internally, it uses CGL to set up OpenGL. It's much simpler to use than CGL itself, especially when you're not just drawing full screen. NSOpenGLView is even easier to use, but (of course) not as flexible.

You need an NSOpenGLPixelFormat in order to create a new NSOpenGLContext instance.

