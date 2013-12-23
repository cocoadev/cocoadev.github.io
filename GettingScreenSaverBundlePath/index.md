---
layout: page
title: GettingScreenSaverBundlePath
---


I'm writing a screen saver, which has a texture stored in its bundle. However, you can't use [NSBundle mainBundle], because this returns the bundle of System Preferences.app. (Or ScreenSaverEngine.app, I guess. Either way, it doesn't work!) How can I get the path the bundle containing the ScreenSaver and its texture?

----

Read the NSBundle docs and apply some creativity. You could do, for example,     [NSBundle bundleForClass:[self class]].

----

Got it! That worked great, thanks.

