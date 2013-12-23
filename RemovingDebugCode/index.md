---
layout: page
title: RemovingDebugCode
---



**Reader's Digest version of this page:**

* Apple's Build styles by default include debugging symbols even in "Deployment" builds, which may cause 10x application size.
* To turn this off, either use pbxbuild install from the command line (Apple plans integrating this into ProjectBuilder some day in the distant future)
* Alternatively, open the "Deployment" Build Style's Build Settings and add "DEBUGGING_SYMBOLS=NO" ("COPY_PHASE_STRIP=YES" should already be in there)

This may give you symbol-less backtraces from your users, though, which you'd have to make readable using the atos command-line tool, though. (UliKusterer 2003-10-06)

----

While browsing though the build options I found this check box:

Build Settings tab -> Compiler Settings -> Generate debugging symbols

which is on by default. Out of curiosity I turned it off, to see what the difference in file size was. To my amazement, a 500k executable suddenly became 40k. That's right, a 10x reduction in size.

Note: You seem to have to do a clean (broom button) after changing this setting for it to take effect.

At first I'm thinking something's wrong, but the app still works. Obviously, you can't do a debug run without the debugging symbols but for the public release this is great.

So, why are heaps of simple Mac OS X programs over 100k when they could be much smaller to download? Am I missing something?

-- PeterMonty

----

You're not missing something. Removing the debugging symbols really does dramatically decrease file sizes, and it's legit. The gcc is horribly inefficient at it, and so this is just a part of life we shall have to live with unless we campaign effectively to The Masses about the proper removal of all unneccessary debugging symbols from shipping products. God speed us on that noble quest.

-- RobRix (it's 4:34 AM, what do you want, Shakespeare?)

----

Also inform them that the stripping of debugging information can be made post-build using 'strip' on the command line.

-- DavidRemahl

----

I am told that **strip -S** (note capital S) will remove the debugging symbols from your app, while still leaving things like function names, so you can get meaningful stack traces if your app should crash on an end-user's system.

-- StevenFrank

----

Debugging symbols are removed automatically when you build with the "Deployment" build style, methinks.

-- FinlayDobbie

----

Hmmm... "Deployment" sets COPY_PHASE_STRIP to "YES" while "Development" sets it to "NO". This should be removing (stripping) the debug symbols but when I try it, an app's size goes from 224k to 220k. Turning off the creation of debug symbols drops it to 64k. So it definitely isn't as effective as the checkbox.

KritTer, deleting docs makes more sense in your case. I've got a 30 and 60GB  drives in my tower. Network speed is my problem.

-- PeterMonty

----

While the Deployment build style in PB has a setting for "COPY_PHASE_STRIP = YES", this does not strip the debugging symbols for you if you are not doing a copy phase to another location for installation (see the "Project Builder Build Settings" under the "Project Builder Release Notes" for more info on these settings). If you are simply building your app and taking it from the build products directory when done, the debugging symbols are not being removed.

While you can manually change the checkbox under the "GCC Compiler Settings" of the "Build Settings" for each build, you can add a line to both the Development build style and to the Deployment build style to manage the debugging symbols setting for you.

Simply add "DEBUGGING_SYMBOLS = YES" to the "Build Settings" of the "Development" build style and add "DEBUGGING_SYMBOLS = NO" to the "Deployment" build style. Apple should have done this by default, but they didn't. Don't know why not. Note that you may have to clean your target before rebuilding to insure that all object files are removed and rebuilt to get rid of all the debugging symbols.

-- BrockBrandenberg

----

The correct way to do a release build (with optimizations and stripped symbols) is to use "pbxbuild install" (see link below). Apple has stated that they plan to integrate this into ProjectBuilder, but for now you have to do it from the command line. There's no need to change the supplied build styles for this purpose. In fact it can be very useful to have symbols in a Deployment build to get stack traces of bugs.

My recommendation is to ship stripped builds (built with pbxbuild install) to customers, but run an unstripped build yourself. That way it's easier for you as a developer to get good stack traces and find bugs faster if you crash your own app. If a customer is experiencing crashes that you can't reproduce, send him/her an unstripped binary (it's important to use the same buildstyle, probably Deployment, but built within ProjectBuilder) and see if he/she can reproduce the error. With CrashReporting turned on (done in Console preferences) you might get a complete stack trace that'll direct you to the name of the method where the crash occured.

----

You can use atos (Address To Symbol : check the man page for details) with the symbol-less stack traces you get from CrashReporter if you've kept an unstripped version around somewhere locally.  If you get CoreDumps from your stripped version, you can load that into gdb with your unstripped version and get decent stack traces.  ++MarkDalrymple

----

There's one thing I'm still unclear on, if I uncheck the "Generate Debugging Symbols" checkbox and set DEBUGGING_SYMBOLS = NO, will I still get stack traces after a crash (possibly from users, etc.)?

- FranciscoTolmasky

----

How does a user get a stack-trace, anyway? I mean, as a developer, I can get one via the debugger. I came to OS X relatively recently from KDE on linux -- where when a program crashed you automatically get a stack trace, with convenience mechanisms to send it in an email or post it to the KDE bugtracker. 

If your application crashes, the user can press "Send report to Apple" and then copy/paste the output to email without actually sending it to Apple. --FriendlyArt



FranciscoTolmasky, read above the answer from MarkDalrymple, which I reproduce here:

You can use atos (Address To Symbol : check the man page for details) with the symbol-less stack traces you get from CrashReporter? if you've kept an unstripped version around somewhere locally. If you get CoreDumps from your stripped version, you can load that into gdb with your unstripped version and get decent stack traces. ++MarkDalrymple

----

atos will not integrate with Xcode for real time decoding via exception handling on the iPhone as it can with OSX (as there is no /usr/bin/atos on the iPhone).   Therefore, I wrote a open source shell that you past an entire stack trace from Xcode into and it prints back the decoded stack trace (using atos).

Announcement/Documentation:  http://myutil.com/2009/2/6/announcing-ratos-a-shell-to-decode-iphone-stack-traces
The code: http://github.com/face/ratos/tree/master

