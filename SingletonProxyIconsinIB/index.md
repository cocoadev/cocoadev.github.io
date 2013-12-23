---
layout: page
title: SingletonProxyIconsinIB
---

InterfaceBuilder does on occasion show some proxy icons, like shared user defaults, font manager etc.

Is there any way I could have it show my singletons, so that I can connect to these?

For example I have a FindManager singleton which brings up the (shared) find panel, so I'd like to bind the *Find...* menu item to an action method of this singleton instance, similar to how *Show Fonts* is bound to     [[NSFontManager sharedFontManager] orderFrontFontPanel:sender].

Currently I'm stuck adding all sorts of     orderFrontXYZPanel: to my application delegate, and then let that class have the logic to retrieve the singleton instance and forward the message.

Of course this latter approach has the advantage of "lazy" instantiation of singletons, but often they are needed right away, and actually in one case, it *must* be instantiated at application launch.

----

Make it so that if someone tries to alloc/init your class, it returns the singleton instead of creating a new instance. Then you should be able to instantiate the class in IB, and your code will take care of making sure that it's pointing to the actual singleton instead of a separate instance.

*Just what I needed! Thanks a lot!*

----

You'll probably need to override     initWithCoder:(NSCoder *)decoder in addition to the 'normal' init methods for your class for this to work with an IB-instantiated object. You might also want to look into making an IB palette for your controller as well.

