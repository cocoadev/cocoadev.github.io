---
layout: page
title: SharingFrameworkBetweenApplications
---

I have my main application, and within this application (in its Resources folder) I have another application. I have a framework that is used for both applications but I have the framework in both applications, making duplicates of it. Is there a way I can share one framework between both applications?

Know what I'm talking about? Could someone help? Thanks. --KevinWojniak

Well, you can probably make the build system do the duplication for you, but you'll still end up with a copy in the Frameworks folder of each application's bundle. I don't know if I can see a way around that without putting them in a global location. But then, I'm having problems with this whole thing myself... I'm not quite clear on what is possible with the linker.

-- RobRix

What about using a symbolic link instead of a real copy?

**Forgive my ignorance... will symlinks work properly relatively?**

----
Yes, symlinks do work!

----

Symlinks are very un-Mac. If the user moves the applications relative to eachother, the symlinks will break, it's not how applications should work on a Mac. A better way to do this could be to use     [NSWorkspace absolutePathForAppBundleWithIdentifier:] to get the path to the application which contains the framework, and then load it dynamically with     [NSBundle bundleAtPath:] (see DynamicallyLoadFrameworks).
.

--TheoHultberg/Iconara

