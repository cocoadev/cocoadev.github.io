---
layout: page
title: ZeroLink
---



http://developer.apple.com/documentation/General/ReleaseNotes/General/DeveloperTools/General/ZeroLinkReleaseNotes.html

General/ZeroLink is a new feature to speed up turn around times for applications. This feature can be turned off if you are linking against a static library. It's easy to get confused as to weither or not General/ZeroLink is enabled when using a target inspector because the project settings will override target settings (for General/ZeroLink). You have to turn General/ZeroLink off in the project inspector (see General/BuildStyles) if a static library is not linking properly. 
Please note that General/ZeroLink is gone with General/XCode 3! 

see also General/XcodeProjectAndTargetSettings

see also General/ApplicationLinking

Developing with General/ZeroLink: small applications and launching on other computers:
http://developer.apple.com/qa/qa2001/qa1322.html

----

**Discussion**

I think it might be possible to link against static libraries with General/ZeroLink enabled if you create a build phase (Project->New Build Phase->New Frameworks & Libraries Build Phase) for static libraries that are prefixed with lib and have the ".a" extension, but I have not been successful at doing this. General/ShamylZakariya  has discussed doing something like this, so hopefully he can verify if this is possible. 

----

I have found it useful for projects that I have built as purely General/XCode. I have seen build times decreased significantly (1 second is normal) but application processes are a bit laggy, but that is totally expected. For better debugging and allowing my applications to 'feel good' I use deployment in most cases or development with General/ZeroLink turned off.

----
*
I just wish Apple had provided slightly more documentation for people to handle the transition.

Here's how you get General/XCode to link a program against a static lib that is a target in the same project. In case you're wondering why -- I have a number of projects that build embedded frameworks for another app (in a separate project). Since I'm a believer in testing everything, thoroughly, I compile also a static library and a number of executable target test-cases which evaluate an aspect of the framework. Since the framework's built to be embedded, I have to link against a static version ( e.g. libname.a ). So, in PB, I'd just make the static target, and while my current target is the test-case, I'd just click on the little bullet in the files pane that equated to "include this in the build".

In General/XCode, however, you have to make a new "Frameworks and Libraries" build phase, and then drag the library into it. I figured it out by *guessing*. From the Info panel, make the library a dependancy of the test-case, so compilation is done in order. And whammo, it works. Admittedly, it actually makes sense... but I wish I had actually found documentation to clarify this difference in project management. Too bad.

For what it's worth, today I printed the "Release Notes" and read them over dinner -- all 20 or so pages. Lot's of material, but it helped quite a bit.

*

**I'd hazard the suggestion that it's a good thing that you *could* figure it out by guessing--better than if you couldn't, anyway. I don't think the UI is really bad, just different enough.**

----

**Several threaded discussions involving usability of build products, generally related to General/ZeroLink and linking issues**

I'm having some trouble with the build folder. My application won't open when I have deleted the build folder, what do I have to do?

----

Are you attempting to open your executable application that was built or the General/XCode/General/ProjectBuilder project? Are there any external resources that your application uses that may have been located in the build folder?

All you should have to do is clean your project and rebuild. Is this a General/ZeroLink build?

----

I'm using General/XCode, and I think all my resources are in the application package. I've tried to clean the project, and I still can't open the app.

----
This is what i see in the Run Log:
*

*2004-02-04 15:37:38.751 General/NewApplication[4415] Unknown flag change detected: 100108
* General/NewApplication has exited with status 0.

*

----

Could be a bug in my code, i'll look into it! Thanks anyway!

----

Bring up Project > Info, and change "Development" to "Deployment". That'll build the standalone application. See General/ZeroLink.

----

**Don't manipulate General/ZeroLink in Build Settings if all you need to do is create a Deployment Build**

After building my General/XCode project (successfully) there is a directory in my project folder called "build".
In that directory there is a path General/MyCFBundleName.build �> General/MyCFBundleName.build �> Objects-normal �> ppc

(where General/MyCFBundleName is the name of the app and project).

I have traced the weird problem to these files, created during the build process. In the ppc folder is a list of files:

General/SomeObject.o

General/SomeObject.ob

General/SomeController.o

General/SomeController.ob

main.o

main.ob

If I remove any of the .ob files, my application is no longer double-clickable to launch in the Finder.
It bounces in the dock a couple of times and vanishes. (If I remove them all, this vanishing is incrementally faster.)

As long as those .ob files are there, I can launch my app from the Finder, and all designed functionality in the app is present.
But I want the application bundle to be independent of those. What sin have I committed?

Note: I created a second nib file to hold features for a customized About box for the app. Perhaps I screwed up something with that.
Everything about displaying the About box works when the build folder structure is intact.

I have built several other simple Cocoa apps, and at least some of them did not have this problem
but that may be pre-General/XCode.

About what am I clueless?

----

This is General/ZeroLink -- basically the executable of your project is not linked (the process of concatenating the object files, more or less), so it will dynamically load the required objects on demand, meaning that if you delete any, it will fail to work.

----

But I have General/ZeroLink turned off for this target. The comment SPECIFICALLY says that ZL should not be used for deployment builds.

----

Are you absolutely sure this is not a General/ZeroLink problem? it sounds *exactly* as what would happen if General/ZeroLink was enabled -- I often have problems with Xcode that even though I switch to Deployment - not all the deplayment actions are executed, for that I need to invoke pbxbuild from the command line.

----

How do I switch to "deployment" mode?

I am talking about the settings in the "Target" inspector.. You know, the one with the four tab views labeled "general" "build" "rules" and "properties".

There's a very subtle hint that this is not the place to do it, because General/ZeroLink looks like it has a line through it....

I hunted around for the panel where I set deployment vs. development mode.

Finally found it by selecting the top level group for my project and choosing "get info" from the Project menu ("Project inspector").
There, under the Styles tab, Zero link was checked.

Thank you for making me look a little farther. There seem to be two places to set this, only one of which is the correct one.

----

This is true of *every* build setting. Each build setting can be set in your target, and overridden in your build style. It's not just General/ZeroLink, it's everything. The line is the clue that it's overridden in the build style.

The fact that General/ZeroLink is generally controlled in the build styles is well-documented.

----

In what sense is the feature is "well-documented" in any sense that  tells
me how General/ZeroLink was so precisely related to the problem I was having?

----

I just wanted to back up my claim of "well-documented". Open Xcode's documentation window, change the search to a Full-Text Search, and type in "zerolink". The first search hit (for me, at least) is the "General/ZeroLink Release Notes", which says General/ZeroLinked apps generally can't be copied around because they refer to the original .o files, exactly like you found. The next hit is "Xcode Help: Speeding Up the Build Cycle", which contains a section on General/ZeroLink that says exactly how to turn it off. Granted, this is not as helpful if you don't know to look for General/ZeroLink-related stuff in the first place, but these are also covered in Xcode's release notes, which you should make a point of reading whenever a new version of Xcode comes out.

----

I'm having the OPPOSITE problem: I can't get my Deployment style to build unless I have Zero Link turned ON. If it's OFF then link fails with the spectacularly unhelpful "Undefined symbols:" without listing any symbols at all. After a day's worth of banging my head against the wall working through the interactions between the target settings and the build styles settings, I'm comfortable I understand how they work and what all the odd little subtleties mean (bolded, strike-through, etc.). I can say with complete confidence that building with Zero Link off fails and with Zero Link on succeeds.

I tried deleting my Deployment style and reconstructing it from the ground up under the theory that something was corrupt or not visible; that failed. I tried matching all the settings in both target and build style to those supplied in the template appropriate to my project. (Matching means EVERYTHING, including strike-throughs and bolding.) That failed. I have never got my link to work with Zero Link off.

I'm on General/XCode 1.1 300.0, 300.0, 300.0.

----

You need to show the General/BuildLog. The undefined symbols will show up there. Most likely you aren't including a library that you are using; General/ZeroLink doesn't care because it finds this stuff at runtime, but when building normally, you have to link against all of the libraries that you intend to use.

----

Hey, imagine that: a build log. Quite handy. Problem solved, thank you.

and see General/XCodeBafflement  for more discussion

----
General/ZeroLink (as of Xcode 2.4.1) has issues (most likely a race-condition) when looking up symbols from multiple threads at the same time (e.g. when creating an instance of a class for the first time). If you sometimes see your -init-messages returning nil in multi-threaded sections (although you're certain that that cannot happen), this is quite likely what you're running into. Filed as rdar://problem/4862371.
