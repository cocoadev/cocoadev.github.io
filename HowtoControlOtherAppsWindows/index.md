---
layout: page
title: HowtoControlOtherAppsWindows
---

How can I control the windows of other applications?  I am attempting to write a true virtual desktop pager in Cocoa that will work like any standard UNIX virtual desktop pager (as in KDE, Gnome, blackbox, etc.)  Workspaces can be added or removed, each workspace shows an outline of the windows in it, and windows can be dragged (in the pager) from one workspace to another.

One problem is that I cannot just do this at a process level.  Hiding and un-hiding processes would work, but what about applications with multiple windows such as mozilla (or terminal!)  I wouldn't want to limit a user to having all his terminal windows in just one desktop.

I've searched through all Apple's documentation, but I can't seem to find the right API to manipulate the windows of other applications.  I'd prefer to use a standard documented API, but if there isn't anything available, I'd be willing to use a private API if it will help me get the job done.

----

So...something like Space.app (check SourceForge)?

I have no idea how to do this, but you might consider adding to Space.app's code instead of rewriting. Oh wait, I think it's Space.dock now...

-- RobRix

----

Space.dock doesn't really do this - it just hides / shows certain sets of applications via AppleEvents, so I doubt its code will help much.

-- JensBaumeister

----

Yes, I took at look at Space.dock, but it doesn't really provide virtual desktops, it just groups applications so that they can be hid/unhid together.  I took a look at the code but it didn't seem to have anything I could use.  In fact, Space.dock's limitations are a significant driving factor behind my desire to write this app.  :)

I think I could complete the app if I knew how to get a pointer to other applications' window objects, but I don't know how to do this.  I'd also be interested in hearing any suggestions for an alternate approach to designing a virtual desktop pager app.

Chris - cmason@unixzone.com

----

Ah, okay, I wasn't aware of that limitation of Space.dock (it's been a while since I've used it). Well, I'm not sure what to suggest...how about you, Finlay? You've got the every private API in Mac OS X memorized by now, don't you? :D

-- RobRix

----

I think that this would have been possible using a rootified app before Apple's Security Update... There was a time when root had unlimited privs in the windowserver, I think (not really sure, I never thought about it at the time, might be interesting to investigate, but not really all that useful these days).

I was interested in doing this at some time, and Apple was extremely unhelpful. There is only one CGSConnection (CGS is CoreGraphics Services, the WindowServer SPI) that can manipulate any window's properties, and it belongs to the Dock. I'm not sure how the Dock sets itself up with this privilege, and Apple is not willing to disclose how it is achieved.

The only thing I can suggest is filing a bug with BugReporter asking for Apple to implement a way of doing this and open up the windowserver API. You might also like to use otool on the CoreGraphics framework somewhat, if you know PPC assembly and some time on your hands (people have figured a few things out).

There is a function to list all the windows in the system and access their properties, as accomplished by the Quartz Debug application, poke around with CoreGraphics if you have the skillz (I certainly don't). /System/Library/Frameworks/ApplicationServices.framework/Versions/A/Frameworks/CoreGraphics.framework/CoreGraphics

-- FinlayDobbie

----

With regard to modifications of graphical elements not restricted to an application's window, could anyone enlighten the crowd on how ShadowKiller is implemented? Could the author, SlavaKarpenko, be kind enough to explain the implementation of the app, or even release the source code and become the true mentor of OS X interface hacking? 

Ehh... Deducing from observations when certain apps crash, the border shadows displayed around all the windows on screen, the dock and the menu bar seem to be one system-wide process.. ShadowKiller seems to simply disable (== temporarily kill) this process while 'doing its stuff'. Don't put me down on this tho. 

So, still no clue as of how my app might interact with different windows managed by the windowserver, windows managed by another app, or on anything that is 'inter-application'-like, excluding Applescript. I'd just give up, if only the mechanics of Quartz Debug were not so luring...

IloPark

----

I'm the developer of SpaceDotApp. As Rob mentions, the source of the program is open, and I would welcome assistance in developing any of the features expected in a virtual workspace program. The current schema of hiding/unhiding apps is the child of necessity given that much of the relevant API remains unpublished, and not my idea of how things really should work. I am currently working on ferreting out the HotKey API. Any pointers would be appreciated.

RileyLynch, riley@codeclever.com

----

IloPark: no, ShadowKiller doesnt kills any processes. It uses an undocumented call to CG Server that disables shadows.

You cannot control an another app's windows from a user-level process, unfortunately.

You _can_ get a list of all windows, but if you ask CGS to do something with them, you'll get a persistent "no". =)

-SlavaKarpenko

----

You can, Slava, you just need to register as the Dock. It might be possible to temporarily register as the dock and then register the dock as the dock again immediately afterwards, not sure. I think the call you'd be wanting to investigate as CoreDockRegisterDockOwner in HIServices.framework.

-- FinlayDobbie

----
I know nothing about how to implement this, but I know that I am just now trying out a commercial app offering just this; managing several workspaces with windows from the same app spread all over.. Look att CodeTek's VirualDesktop. http://www.codetek.com/php/virtual.php
-- EnglaBenny

----

As of Mac OS X 10.2 there's a sanctioned method for this and more:  
the assistive API -  http://developer.apple.com/documentation/ReleaseNotes/AssistiveAPI.html

(or from apple script the UI scripting beta - http://www.apple.com/applescript/GUI/)

- KenFerry

Does that require the user to explicitly enable the accessability API?  If so, it's not on by default, and some users may balk and turnign it on, and/or you'll need to be prepared for the support burden of users not having it turned on and thinking your software is broken.

----

It does, but it is easy enough to check if it is off and redirect users to the preference pane to turn it on if it is. Even Apple does this, for instance for some of the speech recognition features. You could also use APE or similar to do control the windows, or (as mentioned above) register as the Dock (look for the private APIs with Universal Connection in their name). Has anyone found a polite way of getting the Dock to give up its universal connection? The only way I can find is to force quit the Dock and grab the universal connection when it's not looking (which prevents the dock reloading).

SamTaylor

----

There's an open source project up on sourceforge.net that looks much more like the window managers I've used on Unix boxes than Space.app (or Space.dock): http://wsmanager.sourceforge.net/

I looked at it briefly; it's a mess of CoreGraphics calls, unsurprisingly, and well beyond my ken as a beginning Cocoa developer, but you should be able to see how it actually works and decide if you want to try it yourself.

SteveCook

----

Also, the author of the above project seems determined to make all core functionality available as a framework.

It seems to be implemented as code injection into the Dock process.

Joakim Arfvidsson

----

Just in case anyone is curious, there is a (partial) header for CoreGraphics Services floating around - CGSPrivate.h 

While it's incomplete, much useful information is to be found in there, such as the "Universal Owner" mechanism by which the Dock is allowed to make privileged calls to the Windowserver. You can do some funky stuff, like disable Expos�.

SimonParsons

----

Does anyone have a link to this?

----

CGSPrivate.h is in the source of DesktopManager (at least the version I have which was DLed a while back).  The URL again is http://wsmanager.sourceforge.net/
It might just contain stuff that DesktopManager uses though.  All of the transition effect calls are in there.  Nothing cooler then the CGSTransitionType enum. :)

Doug McInnes

----

I'm playing around with Cocoa (extremely new to programming on OS X!). I'm wondering if there's some way for one app (call it BounceWindows) to get and set the position of windows owned by other apps. The idea being that this app could open, and shift other windows around the screen randomly, and bounce them off the edges of the screen, and off each other if they make contact.

I've done similar things in Windows, but is this possible using Cocoa (or some other OS X framework?)

----

You might want to check out the assistive device api, it lets you access the frames of all windows, and it will happily notify you whenever a window's frame changes.

Check out http://developer.apple.com/ReleaseNotes/Accessibility/AssistiveAPI.html

*However, be aware that support for assistive devices must be turned on in the Accessibility preferences pane and, last I checked, there's no way to enable it programatically. You'd have to tell your users to 'turn this on'. That's kind of clunky in my opinion.*

----
It is clunky, but it's intentional. Without this precaution, it would be trivial to write a piece of spyware that could read text out of any open window in your login session.

----

Quoting Slava:

*You _can_ get a list of all windows, but if you ask CGS to do something with them, you'll get a persistent "no". =)*

Okay... but how can you get that list? All I really need access to is which screen they're on, and their frames. Is that much possible?

----

Quoting FinlayDobbie:

"I think the call you'd be wanting to investigate as CoreDockRegisterDockOwner in HIServices.framework."

where is CoreDockRegisterDockOwner defined exactly, or what's its prototype? Is it Leopard specific? I am not able to find it anywhere. 

-- EttorePasquini

----

// Get on-screen window counts and lists.
extern OSStatus CGSGetWindowCount(const CGSConnection cid, CGSConnection targetCID, int* outCount); 
extern OSStatus CGSGetWindowList(const CGSConnection cid, CGSConnection targetCID, int listSize, int* list, int* numberOfWindows);

// Get on-screen window counts and lists.
extern OSStatus CGSGetOnScreenWindowCount(const CGSConnection cid, CGSConnection targetCID, int* outCount); 
extern OSStatus CGSGetOnScreenWindowList(const CGSConnection cid, CGSConnection targetCID, int count, int* list, int* outCount);

You could then follow that up with a CGSGetWindowBounds() once you've found the window you are searching for, or a CGSGetScreenRectForWindow() to figure out more about the screen.

These functions are all private; you can see more about them at CoreGraphicsPrivate

----
Hey, I'm looking to perform a slightly different scheme. I have two apps communicating through IPC. One of the apps needs to display a window in the other app. So a message is sent to the other app, which then wants to take focus from the first app and display its window. Can one app relinquish the focus and give it to a specific app? As far as I can tell, you can hide other applications with NSWorkspace calls, but this seems a bit overkill. Any advice? -JeremyJurksztowicz

----

The two different apps have their own protected memory spaces, etc. There's no way to just move a window from one to the other wholesale. Are you sure you need the other app to own that window? Perhaps you can continue using IPC for this task.

----

You can do this with     [NSApp activateIgnoringOtherApps:YES]. However, the second application will become the foreground application in all respects, and so it will not seamlessly appear to be part of the first application.

----

All that's going to do is make his application the foreground application.

----
Sorry, I think I wrote that wrong. Your solution is exactly what I was looking for. I did not want to literally move a window from one app to the other, but rather just wanted to have app1 relinquish focus so that app2 can display a window selected by app1. Perfect. You gurus just made my day :). JeremyJurksztowicz

