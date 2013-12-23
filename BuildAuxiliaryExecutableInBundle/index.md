---
layout: page
title: BuildAuxiliaryExecutableInBundle
---

I'm developing a Cocoa app that has an associated Foundation server daemon.  I've configured the server target as a dependency of the app, and created the appropriate Copy Files phase to put it in Contents/MacOS.  It all compiles fine, but my server needs to access managed object models stored in the bundle.  No problem when I build the app and run the server located in Contents/MacOS, but I can't use the IDE to run/debug the application because it's built in /build, outside of the bundle.

How can I tell Xcode to build my server target in-place in the bundle, rather than in /build?  This seems to be a bit of a chicken-and-egg problem, as the app bundle isn't guaranteed to exist if I'm just compiling the server target, but it makes testing quite difficult otherwise.

----

I think you're approaching the problem from something of the wrong angle. You can't change where it gets built. However, you can make it get copied into the bundle when you build, by using a Copy Files build phase in the server's target, then run that copy for testing by creating a Custom Executable in your project.

