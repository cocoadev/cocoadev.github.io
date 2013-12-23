---
layout: page
title: XCodeTreatingHeadersAsResources
---



Has any one noticed Xcode 1.5 treating header files as resources? By resources I mean they are getting get copied into the Apps Resources directory by default during a build. If this is localized to my install, how do I fix it. Every header I add ends up in the targets 'Bundle Resources' step. Help!

Thanks


----

This happened to me too; all I did was remove the headers from the target and then clean and rebuild.  -- Bo

----

Ouch, well thanks for the confirmation that I am not the only one. Unfortunately, I prefer to have my headers as part of my project.
----
Have not had that problem. If you select the target, you can set the behaviour for headers (private, project, public, I think are the choices).
----
Hmm, I seem to recall that for controlling the copying the headers to a built frameworks Headers directory. This is moving them to the Resources directory (Including them in the 'Build Resources' step) by default. I don't even know where the public/project/private setting is in 1.5. I must be going blind. Note, this is a straight Cocoa based document application.

----

Just to clarify, I didn't remove the headers from the project, just made them not associated with the target.  -- Bo

----

I just ran into this problem today too. Very annoying.
----
Well, I ran the issue across the Xcode-users list and it appears to be a bug. It has been filed by another as rdar://3761632

----

I think this has been fixed in Xcode 2.1. Anyone else want to confirm this? Err maybe it was 2.0...

*nope, it still happened in 2.0, so if it is fixed, the fix came in 2.1*

It was a bug in the project templates, so my bet is that they fixed them when they redid them as .xcodeprojs in 2.1.
