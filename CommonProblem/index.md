---
layout: page
title: CommonProblem
---

**Common Cocoa Problems**

This page is a listing of common problems experienced while learning Cocoa or simply working with it.

If you're experiencing a problem and you think it might not be unique, check here first. If there's a page about a problem that you think is a common one, but it's not here, add it to the appropriate category below.

Please try to keep the page organized with simpler topics at the top and more advanced topics at the bottom. And please keep in mind that it's intended to be a list of common problems, not a master list of every problem/solution page on the site.

----

**Memory Management**

The bane of the newbie, and commonly the bane of the non-newbie. Without good memory management practices, your app will likely leak like crazy, crash at random, or both; but how do you track down these problems and fix them?


*MemoryManagement - *The* page with links to everything imaginable about the topic.
*DebuggingAutorelease - Crashing in NSP<nowiki/>opAutoreleasePool, can't find that extra release that shouldn't be there, this page is for you.
*NSZombieEnabled - A useful technique for autorelease bugs and just plain over-release bugs.
*MemoryLeaks - The opposite problem: not enough releases, and how to track them down.


----
**Why did my program crash?**

*SendEventCrashes - Application crashing while NSApplication is trying to send an event     sendEvent:
*SignalsSentOnCrash


----

**Interface Builder**

InterfaceBuilder is crazy powerful, but sometimes there are things you want to do, or problems that it causes, which are not obvious.


*IBOutletNSViewIsNil - You hooked up an outlet, but it doesn't work because it's nil, even after the nib loads. Here's how to fix it.
*MakingNibsTalkToEachOther - You have two nibs and you need them to communicate. Some general techniques on how to solve this.
*MenusAndDocuments - The menu bar is in MainMenu.nib, but you want to make a menu that talks to MyDocument.nib. This is how to do it.


----

**GUI Stuff**

Some GUI elements are everywhere in OS X, but they're not always easy to create.


*DrawRect - Your view draws strangely when a subview is clicked, or otherwise things don't appear how they should. You may have implemented this method wrong.
*BorderlessWindow - How to create a completely custom window, without the standard title bar, with weird shapes or transparency.
*NSToolbarSampleCode - For the longest time, InterfaceBuilder has no built-in way to create a toolbar, so you had to do it in code. This page shows you how to do both.


----

**Inter-Process Communication**

How to start and interact with other programs from yours.


*WrappingUnixApps - If you have a UNIX command-line program whose functionality you just have to include in your Cocoa GUI application, this page tells you how to do it.
*NSWorkspace - A very useful class which, among other things, provides information about other running applications, and also lets you launch applications and open URLs. (see also NSWorkspaceNotifications, a rich set of signals provided by the NSWorkspace notification center.)
*NSAppleScript - How to embed AppleScript in your application so you can manipulate other apps.
*HowToSupportAppleScript - The other way around: how to support AppleScript, so users can script your app.
*DistributedObjects - For client/server type communication


----

**Compilation and Linking**


*ParseError describes some common ObjectiveC-related errors
*ApplicationLinkingIssues refers you to several topics related to, you guessed it, ApplicationLinkingIssues


----

**Audio**


*Because of its hefty learning curve, lots of AudioQuestions get asked here about CoreAudio (not actually a part of Cocoa API)
*Try CoreAudioAndAudioUnitsTutorial to see if it can point you in the right direction on this unwieldy matter


----

**Discussion**

Here's my first attempt at this page, and I hope it's helpful. I tried to pull from both my own experience with things where I had trouble, and things where I see people on the site (and other places) getting into trouble frequently.

As I stated above, I tried to organize things with the simplest stuff at the top, and the more complicated things at the bottom. When possible, I linked to the "howto" page rather than the summary/link-to-docs page.

If anybody has suggestions for changing the page, removing things, adding things, etc., post them here, or since this is a wiki, go ahead and make the changes yourself. -- PrimeOperator

