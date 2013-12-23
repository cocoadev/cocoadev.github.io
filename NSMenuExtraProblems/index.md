---
layout: page
title: NSMenuExtraProblems
---



Hello,

I want to create an NSMenuExtra. I followed the tutorial on cocoadevcentral.com, but when I try to run it, it gives this error:
ZeroLink: unknown symbol '_main'

I am guessing that I am not setting up the XCode project correctly, but I'm not sure how to fix this. Can someone help?
----
Uh... do you have a main() function?
----
What do I need to put in the main() function for an NSMenuExtra?
----
You don't need one, the menu extra is loaded and run by the SystemUIServer. Make sure your build settings are correct; the important ones are "Mach-O Type", which should be "Bundle", and "Wrapper Extension", which should be "menu". (In fact, you may even want to create a new target of type "Loadable Bundle" and change the Wrapper Extension setting). Your product should then be *W<nowiki/>hateverMenuExtra*.menu, and when you open it (not run it) the menu extra should show up in the menu bar.

Unfortunately, you can't run or debug menu extras from within Xcode. You have to reload them in the menu bar every time, which means restarting SystemUIServer ("killall SystemUIServer" as a shell script / Terminal command will do it) in order to load your new code. Any output (e.g. NSLog) goes to the Console application (/Applications/Utilities/Console.app). This, of course, makes creating an NSMenuExtra a LOT harder. --JediKnil
----
Thanks, I actually was creating an app instead of a bundle, so I fixed the problem.

