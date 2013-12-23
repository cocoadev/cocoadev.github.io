---
layout: page
title: DebuggingScreensavers
---




Having a hard time debugging a Screen Saver plug-in from GDB because the screen saver covers the entire screen? You can run the screen saver in "debug" mode, which forces the screen saver to draw behind all other windows (except the Finder's desktop). This makes debugging a screen saver plug-in possible w/o telnetting in from a remote machine.

To do this, start GDB from the command line:

local> **gdb /System/Library/Frameworks/ScreenSaver.framework/
Resources/ScreenSaverEngine.app/Contents/MacOS/ScreenSaverEngine**
(space added after .framework/ so the path doesn't make browser windows hugely
wide)
[ ... ]

At the GDB prompt set your break points (if any) and then run the engine with the "-debug" flag:

(gdb) **run -debug**

You can combine this with the "-module" flag to force the engine to look up a specific module by name rather than simply use the default:

(gdb) **run -debug -module Basic**

-- MikeTrent

Also, if you have two machines, you can ssh in with the other machine and run gdb from there.

----

The **-debug** option causes the screensaver to run on the desktop and exit when the mouse moves on the desktop. The **-background** option keeps it from exiting when the mouse moves.

Not sure how to break on a line in not-yet-loaded code (future-break couldn't find the source file), but you can always add a break on a Cocoa method. For example, to break on a particular line, you can add:     (void)[NSString string] to your code and then:

(gdb) **b +[NSString string]**

----

A ScreenSaverView is a subclass of NSView, for testing purposes you can set up a window with a custom view that adds the your ScreenSaver view to itself as a subview, you then send it the startAnimation message. This way you can run the screensaver in your own environment which will take care of most of your debugging needs 

----

Wrapping your ScreenSaverView in an plain NSView also works, the NSView initialises the ScreenSaverView and adds it as its subview, this way you can use it in a plain application for debugging, there is no need to override drawrect or send any animation methods to your ScreenSaverView this seems to be handled by the superclass.

----

Add the System Preferences app as a custom executable in xcode, and then just debug as you would any other app. You do have to install your build first, but I suppose there is a way to make xcode do that too.

-- Steve Israelson

----

The very simplest and best way to debug a screensaver is to use SaverLab. Set it as a custom executable, and off you go. It's better than System Preferences because SaverLab will automatically open whatever screensaver was open when you quit it, so you don't even have to click anything to get your screensaver to start running in a nice window.

Is this the author of SaverLab saying that?  I find it nice, but I'd hardly call it the best for all situations; it's hard to attach to, just as System Preferences is, and seems to run stale copies of my code a lot when I'd like it to check and reload.  The console is nice, though, and the step, speed, and record features. -- DanKnapp

----
It's the author of GPULife, ChemicalBurn, PongSaver, and others saying it. Neither SaverLab nor System Preferences are remotely difficult to attach to, and a simple symlink or Copy Files build phase eliminates stale code.

Sorry, that was uncalled-for of me.  Well, I found a procedure that worked for me to deal with the stale-copies issue; I'm not totally convinced that it isn't SaverLab's fault, but I'd rather just retract the statement altogether than spend the time investigating further. -- DanKnapp

----
May I ask what that procedure was? Perhaps it could be helpful to other developers as well.

----
Well, it's not polished and since I was kind of frustrated and didn't try every variation, it's entirely possible that some of these steps are superstition, but since you ask, sure.  To set up for this, turn off SaverLab's feature that re-opens what you had open last time on launch, add SaverLab to the screen saver's xcode project as an external executable, and make it the current one.  Then, each time you want to rebuild: quit SaverLab; choose "run" in xcode, which will re-launch SaverLab; choose "open" in SaverLab and find the just-built copy, which will be under build/Debug/ or build/Release/ as appropriate.  Of course only the quit/relaunch is necessary, the rest is just to make it less tedious.  When I was figuring this out, it appeared that you'd get a stale copy if you used the auto-reopen, although I was at a loss to see how that could be when it's actually been overwritten!  If other people are able to reproduce that behavior and let me know I'm not imagining it, that would be great.  I'm kind of embarrassed to post this since it seems so unlikely and I haven't double-checked it... :)

----
I would suggest using a Copy Files build phase at the end of your target to copy the product to ~/Library/ScreenSavers, which will ensure that SaverLab can load it properly and also that System Preferences and the other built-in screensaver stuff can find it. It will also make sure that these things aren't finding an old copy that happened to be left in that location. You will absolutely have to quit and relaunch SaverLab each time you build since your old code can't be unloaded, so that is definitely an important step.

----
Ah, excellent idea, thanks.  I didn't realize it couldn't be unloaded, either.

----

I would also suggest setting the "Per-configuration Build Products Path" for your Debug  configuration to "$(HOME)/Library/Screen Savers/". I find it a little neater than the Copy Files approach.

