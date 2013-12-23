---
layout: page
title: TrampolineObject
---




An object returned by a method which "bounces" messages it receives on to some other object (typically the object that returned it, as in General/HigherOrderMessaging).

Can anyone think of other uses for trampolines?

I suppose that, potentially, you could use this to isolate a delegate; provide a trampoline (like General/LSTrampoline) that will bounce messages to it, without giving access to its address to anything but the trampoline. Whether that is useful or not is a matter totally other.

It could be, if you want to use distributed objects without having to give the delegate, coming from another process, root access because of some security risk.. but that's pushing it.

Related discussion in  "Trampolines for Nested Functions" in the GNU Compiler Collection (GCC) Internals documentation [http://www.delorie.com/gnu/docs/gcc/gccint_136.html]

"Making a "Do not show this warning again" alert"
[http://www.gigliwood.com/weblog/Cocoa/Making_a__Do_not_sh.html] tutorial on Dan Wood's: The Eponymous Weblog

----

General/BSTrampoline is an example of a trampoline object, used to implement -do, -collect, -select and -reject for collection classes.

General/LSTrampoline is another implementation which is perhaps more flexible in that all it does is bounce messages--the -do, -collect, -select, and -reject methods are left up to the collections.

----

General/NSProtocolChecker is exactly as described above; it acts as a proxy for an object, and only passes messages through that are part of a given protocol.

----

I've seen some implementations of this pattern in the form of a Trampoline that can be used for an object to invoke messages with itself as the target on the main thread.  (General/UKKQueue has an example of something like this)

JKP

*These are basically a form of HOM that doesn't run over collections, but does something else with the message. General/CHOMp, for example, has a     performAfterDelay: HOM which is similar, and adding a     performOnMainThread HOM would be fairly easy.*
