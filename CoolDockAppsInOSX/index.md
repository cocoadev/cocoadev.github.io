---
layout: page
title: CoolDockAppsInOSX
---

I have a question..

Why dosent OS X support (and Im not totally sure if it does not) cool animatable dock apps (like in NeXT). I wanted to know if it was possible to make a dock icon an openGL window with crazy 3D movement in the dock. I have not seen anything like this, and I am wondering why it is not possible (like it is with the openStep dock).

Thanks,
KW

----

Look at     [NSApplication setApplicationIconImage:(NSImage *)anImage]. Anything you can draw in a NSImage you can do in your dock icon.

----

Well, QuickTime movies can update (or play) in the Dock. So is there a way to create a view inside of the icon area that can hold an OpenGL view or QuickTime view??

----

I think you can set an image that only contains a single NSBitmapImageRep and manipulate the raw bytes to your hearts content. You can probably just keep setting the same icon image to get the dock to refresh the icon being set. This will avoid having to allocate an image for every frame or at least use two images to swap buffers (that is, change the icon image in the dock from one image the other icon image). --zootbobbalu

----

Don't manipulate bitmap images' data after creation. The correct thing to do is create a new image for each animation frame. This sucks, but they're quite lightweight. -- JensAyton

----

I don't think that it specifically isn't supported, just that it hasn't been done, up to now...   my team in my software engineering class is making a framework specifically for making it easy to write dock applications.  We will be releasing the framework with source code and a rather nice dockapp similar to wmnet for linux/bsd, but given a facelift for OSX.  Ought to be up in a few months, check back...   http://sourceforge.net/projects/dockapp-osx/

----

There are things like LoadInDock, and ActivityMonitor can show cpu/network/memory graphs as its dock icon

