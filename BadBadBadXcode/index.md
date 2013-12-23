---
layout: page
title: BadBadBadXcode
---

Document Xcode bugs and work arounds.

Anyone know what to do with Xcode not clearing the warnings and errors out of the file gutters on rebuild? *Have you fixed them? Otherwise it's not supposed to - the warnings & errors in the gutters and SmartGroup represent the state of your code, and persist across builds. There was a long post on the subject on the xcode-users list by Chris Espinosa on 17 August. I don't know if that list is archived anywhere though.*

Yes, of course I've fixed them :P , and it says build succeeded, but a VERY long list of errors say stays in the gutter and error window... which is a pain, b/c if you've fixed SOME of your errors you don't know which ones are still active... :( I wish I had seen that xcode-users list.  What was the gist?

----

Excerpts pasted below. Apple's archives are totally hosed right now, so they're not even of the minimal use they usually are. I suppose the gist is 'use the Build Results window.'

...[snippage]
*
This is how the Errors and Warnings smartgroup is designed to work:

- Start with a clean build.

- Introduce errors into file A and file B.

- Build.
    You'll see file A and file B in the smartgroup, with the errors in 
each.

- Fix both sets of errors.

- Build again.
    As each file builds successfully, its errors (and itself) are 
removed from the smartgroup.

When you have a successful complete build with no errors, the Errors 
and Warnings smartgroup should be empty.

Now contrast that with this:

- Start with a clean build

- Introduce a warning into file A and an error into file B.

- Build.
   You'll see file A and its warning and file B and its error in the 
smartgroup.

- Fix the error.

- Build again.
   File B is rebuilt and it and its error should disappear.  But since 
File A built with just a warning, and you haven't touched it, it's not 
rebuilt, and its warning persists in the smartgroup.

This is probably the most common confusion about the Errors and 
Warnings smartgroup.  As a smartgroup, it persists the known status of 
all project files, while the Build Results window gives you the 
this-build-only errors and warnings, which may differ.
*

[more snippage]

*

because of dependencies, etc. in a 
build that has errors, a given build command may not actually instigate 
the rebuilding of all files that have been touched:

- Start with a clean build

- Introduce errors into files A and B

- Build; see A and B in the smartgroup with their errors

- Fix A and B, but introduce an additional error into A

- Build

   File A builds, adding its new error to the smartgroup.  Because of 
dependency checking, B may not be rebuilt, so its errors persist even 
though you fixed the code.

This is the scenario we've seen before and that the engineer thought 
you were talking about.

If you have fixed all bugs and get "Build succeeded" with no errors or 
warnings in the Build Results window, and there are still items in the 
smartgroup, that's a bug.

If you rebuild a file and it shows up in the Build Results window with 
no errors or warnings, but its errors aren't removed from the 
smartgroup, that's a bug.

*

----

Aha! I've done some more digging in my archives and turned up this followup: (Date: Thu, 19 Aug 2004 Subject: Errors & Warnings (and Relative Paths)

*

Since version 1.5 Xcode has problems with projects where the source 
files are referenced by "Relative Path" and the path goes up (e.g. 
../sources/main.cpp).

If you build "../sources/main.cpp" with compilation errors, the errors 
are marked with a red circle.

If you correct the error, the compilation will succeed - but the Error 
Marks do not disappear!


I filed a bug report with a description at bugreport.apple.com (Problem 
ID:  3767854)

*

----

And this further followup by Chris Espinosa:

*

I've confirmed that this is indeed the case: if a source file is 
Relative to Project but not "below" the project's directory in the file 
system hierarchy, errors and warnings will not be cleared in the Error 
and Warnings smartgroup or detail view.  The Build Settings window 
will, however, have the correct build results.

Here are two workarounds:

1) Move the project to the top level of the source, so that all source 
files are peer to it or below its peers (i.e. no ".." in relative 
paths)
2) Use the Source Trees feature of Xcode:

   a) In Xcode Preferences, choose Source Trees
   b) Click Add and add the name of the top level of the project (in 
Setting Name) and the absolute path to it (in Path)
   c) Open your project and select all affected source files, and choose 
Get Info
   d) In the Inspector, choose "Relative to <your Source Tree Setting 
Name>" for the Path Style

All developers sharing this Xcode project file need to set the Source 
Trees setting appropriate to their system.

Chris

*

...I just knew those 3858 unread messages in my developer lists folder would be useful someday ;)

----
Yes, that describes my scenario to the T. .. relative files.  That's a real kick in the head, though.  If you're like me, and you like to develop modules of your program using unit test projects, then there is no good way to make your unit tests avoid relative ../ paths... without really making things messy.  On the other hand, unit testing is exactly when you build and rebuild every 10 seconds, and REALLY want live updates of your errors.  THAT PISSES ME OFF!
----
What is UP with this!?  Whenever you create a new class in xc1.5, you get a shiny #import<Cocoa/Cocoa.h> stuck at the top even if you're in a Foundation Tool template!

