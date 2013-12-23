---
layout: page
title: LinkerCantLocateEmbeddedFramework
---



I am just entering the wide, wonderful world of Cocoa and am trying to figure out how to make an embedded framework. The application is an off-the-shelf Cocoa document-based application with very few changes (pretty much just     Info.plist and     InfoPlist.strings changed), with a framework target (with no files to its name besides     Info.plist and     InfoPlist.strings) added to the project. The framework gets installed into the application bundle (using     @executable_path/../Frameworks as the install path; the framework shows up in     *application*.app/Contents/Frameworks), but I get an error from     ld when building:

    /usr/bin/ld: can't locate framework for: -framework Cognizance

As far as I can tell, the correct framework search path is being used (    -F/Users/john/Documents/Xcode/Cognizance/Debug shows up in the     ld command-line options), so I'm baffled. This happens on both the Debug and Release build configurations. This is a brand-new project, and I've followed the instructions in all of the embedded-framework tutorials I've found (save prebinding), including Apple's own and EmbeddingFrameworksInApplications. Does anyone have any inkling why this is happening?

----

does the app target depend on the framework target?

----

The framework target is in the Direct Dependancies section of the application target, yes... like I said, the framework appears to have built correctly.

----

I figured it out; if there are no source files that are in the framework target, there is no dynamic library created for the framework. And if no dynamic library is created for the framework,     ld can't link against it. Somewhat obvious.

----

Just out of curiosity, why were there no source files in the framework target?

----

Experimentation. I was trying to get a skeletal version compiling before I did much work.

----

If you're getting this problem in 2007 (as I have been), and the symptoms are the same as this person: http://www.cocoabuilder.com/archive/message/xcode/2006/2/23/4298 then try upgrading your Xcode to the latest (currently 2.4.1), linking with the OSX 10.4u SDK, and if you're on a PPC, try turning off intel builds.  This worked for me when I had the same problem.

