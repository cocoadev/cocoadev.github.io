---
layout: page
title: IBActionsInPluginCrashing
---



My app loads several plugins, and each plugin has an NSView which has IBOutlets in it. Now the view gets loaded fine, shows up in the app, but whenever I click a button that loads a nib, it crashes. My plugins work 100% when they reside in the built-in plugins folder, but when loaded from outside of the app, it crashes on IBActions.

I am calling NSBundle's loadNibNamed:owner from within the owner's initialization class and outside of it (i.e. initializing the class and using that class as the owner instead of self) but both times it crashes

Console gives me "objc: thread is already initializing this class!" and the crash report doesn't make sense:
    
Thread 0 Crashed:
0   libobjc.A.dylib          	0x9083be80 _objc_trap + 0
1   libobjc.A.dylib          	0x9083be14 _objc_fatal + 0x48
2   libobjc.A.dylib          	0x908329c0 class_initialize + 0x120
3   libobjc.A.dylib          	0x90831524 _class_lookupMethodAndLoadCache + 0x84
4   libobjc.A.dylib          	0x90831298 objc_msgSend + 0xb8
5   libobjc.A.dylib          	0x908329e0 class_initialize + 0x140
6   libobjc.A.dylib          	0x90831524 _class_lookupMethodAndLoadCache + 0x84
7   libobjc.A.dylib          	0x90831298 objc_msgSend + 0xb8
....


I'm desperate for a solution! Anyone experienced this before? --KevinWojniak

*OK I fixed it. It was a specific error to my project, like I imagined. I had loaded the same class twice from different plugins and somehow that was screwing up everything. I had previously had my plugin's principalClass implement a -name method, which required me to initialize the class to access it. Now I just use an object in its info dictionary. I changed how my app reads in plugins, and now it's even better - more efficient and faster!*
----
I am having the same trouble now. Everything works on Tiger but I get this crash on Panther.

Let me explain:

My application contains several targets:

1. Main Cocoa application

2. Cocoa Plugins

3. Cocoa Library that contains base class for plugins and several classes used by both main application and plugins.


*When in my plugin I try to access any factory method from my library (I need alloc) - I get this crash.*
**Corrected: An actual crash with the same top stack occurs when calling init method. While reading StrangeCrashAtStart I see that the problem could be because I access my class from several places (from my application and from all plugins. But this is what I really need. I need a class like NSString that could be accessed from everywhere.**

It looks like I am missing some basic concept of linking? Please help.

Thanks. --AndreyBabak

----

By "Cocoa Library" do you mean a framework?

**No, just a Cocoa static library**
----

If you need the same class to be accessed from everywhere, then you must put it someplace where it can be shared, and not simply copy its source into all of your components. Stick this class in a framework, or put it in the main application and use ld's     -bundle_loader flag, but do *not* duplicate it, otherwise you will crash and burn.

**Does it mean that if I link to static library from several places the code is duplicated? In fact some classes defined in my static library work ok in several places. I was adding test snippets of code to localize the problem but can't find any logic. It looks like a Panther problem that was fixed in Tiger (as it works there) but I may be wrong.**

**Could you point me to some more information about -bundle_loader?** Thank you --AndreyBabak

----

If you link a static library into several binaries, and then link these together - statically or dynamically - code will be duplicated.

It is in the nature of the dynamic nature of Objective-C that multiple classes with the same name won't work. Tiger has preliminary support for unloading Objective-C classes, but it doesn't actually work.

----

I see. Thank you! I've made a framework and now it's working. Although I'm quite lost with prebinding tuning so I just switched it off as minimal Mac OS X version expected is 10.3.8 --AndreyBabak

