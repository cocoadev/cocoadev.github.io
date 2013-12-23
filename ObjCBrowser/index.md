---
layout: page
title: ObjCBrowser
---

ObjCBrowser-A variation on SmallTalk's Browser application, meant to ease the writing and browsing of ObjC code.  

ObjCBrowser is a project of JoeOsborn.  

ObjCBrowser provides a handy, NSBrowser-based way to look at projects.  It looks something like this:

Image link (doesn't work in OmniWeb): http://homepage.mac.com/being/NormalUsage.jpg

And clicking on anything in any of those columns will provide in the text view either a template or the current value of the selected item-- for instance, a selection path like: 

My Project-->Classes-->Useless Classes-->A Useless Class-->Instance Methods-->Some Method Category-->-eatFood

Will provide, in an NSTextView at the bottom of the window, the implementation of -[AUselessClass eatFood].  It can there be modified, and then cmd-s can be hit and the changes will save.  Class definitions can also be modified in such a way. 

Methods and classes can be categorized by means of a handy interface brought up via a contextual menu.

ObjCBrowser may also eventually feature a preprocessor for a special dialect of ObjectiveCee.  This will allow for such shortcuts as:

    
@{...} for CodeBlocks
@42 for NSNumbers
@(item, item, ...) for NSArrays
@[receiver message] for NSInvocations
0@1 for NSPoints
0@1<>1@2 for NSRects
@<key=value, ...> for NSDictionaries
And any others that seem useful...


After the preprocessor, I'll also implement a Workspace window in which one can enter arbitrary objective-c code and see the result(either in that window or in a Transcript somewhere).  Most coding can be done in a Workspace, and once it works, it can be committed to a proper method.  

Unit testing will be integrated.  Compiling the project and running tests will be the matter of a simple keystroke.

ObjCBrowser will eventually gain RefactoringBrowser features.

A working version of ObjCBrowser is available at http://homepage.mac.com/being, but I make no guarantee as to the extent of its working.  Its serious use is also not wholly recommended, as a new version of OCB is in the works that is a medium-scale rewrite.

*The iDisk link no longer contains ObjCBrowser as of March 2, 2005.  A pity, as I would have liked to try this tool.*

"I would also have liked to have tried this tool. I can't find Joe Osborn anywhere on the web either...*

-- JoeOsborn

---

The .mac link above is now 404.  Is there a new home for ObjCBrowser?

-- MichaelNygard

