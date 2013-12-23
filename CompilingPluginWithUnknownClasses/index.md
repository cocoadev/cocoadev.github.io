---
layout: page
title: CompilingPluginWithUnknownClasses
---



What i'm trying to do is to create a plugin (loadable cocoa bundle) that will be loaded to a cocoa app. When my plugin loads, it does [m<nowiki/>ySubclass poseAsClass:[o<nowiki/>riginalClass class]] and places my subclass as the original class. The only problem is that i can't manage to get my plugin to compile. I tried specifying the path to the original app, which loads my plugin, as the "Bundle Loader" but i'm still getting an undefined symbols error (my subclass is a subclass of a class that the main app owns so it does exist). I also tried to compile using "-flat_namespace -undefined suppress" and it managed to compile, but then i started getting weird random crashes in the main app at places that my plugin has nothing to do with them.

Am I doing something wrong?

What else should i do?

----

It doesn't sound like you're missing a trick, it sounds like you have a bug causing 'weird, random crashes'.  

----

Do the plugin and the app have classes in common? Are you sure you are not loading some classes that are already loaded? This caused me much pain when I was writing an AudioUnit, extremely random crashes, I ended up just moving the shared classes into a framework.

----

Are you in control of the class you are subclassing?

----

Don't suppress undefined symbols; those are *errors* in this situation, and suppressing them won't make them go away.

*They aren't errors.  If you're subclassing a class from the app, the linker will complain unless you suppress the warning.  See GPGMail, or any other plugin that does this.*

The linker will not complain if you pass a proper     -bundle_loader flag, which is the right way to do this.

Using the app as the bundle loader should work. Are you sure that the copy of the app that you're linking against is unstripped and not using ZeroLink?

----

Thanks to GPGMail, i found the answer to my question.
What was needed to do is to specify the path to the "Bundle Loader" and also the flag "-undefined dynamic_lookup" (in the Othe Linker Flags).

