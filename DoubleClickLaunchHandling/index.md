---
layout: page
title: DoubleClickLaunchHandling
---

Hi I've created a non-NSDocument Program that creates custom files that open the application when double-clicked but how do I make the application recongize and handle when the application was started by a file being double-clicked?

Gabe

----

    
- (BOOL)application:(NSApplication *)sender openFile:(NSString *)filename;
- (void)application:(NSApplication *)sender openFiles:(NSArray *)filenames;


----

Thanks for the reply.  I implemented it to the main file but it still does not work.  The *method* is not even called.  Is there something I missed?

*Implement it in whatever class is your application's delegate.*

----

Just one last question.  When I create my files, it comes up with the right icon and kind and everything but when I double-click on it Finder gives me a message that says something like "There is no default application set for....." how do I avoid this and make it launch my app right away?

*You don't. This behavior was added to OS X as a **security measure** to help prevent things from being automatically launched (trojans to fool users) if it hasn't been launched on the system (by that user) before. This is intentional. It should work normally after the first time you double-click that file type and confirm your app. If it's not, there's something wrong (see Apple's support pages).*

Uh, no. The **security measure** is the message that says, "You are about to open Application X for the first time...." "There is no default application" means exactly what it says, and it seems that the poster has messed something up in his file associations.

*The Info.plist is formatted correctly right?*

