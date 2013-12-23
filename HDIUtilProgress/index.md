---
layout: page
title: HDIUtilProgress
---

Hey, I am trying to determine the progress of an hdiutil disk imaging oepration.  I have it properly generating the disk image file through an NSTask, but when using the -puppetstrings verb (as described in the man page) to display output, it errors out as undefined.  I have tried numerous different ways and searched the archives for a solution but I can't find one.  Does anyone know how to determine the progress of an hdituil operation such that can be parsed by Cocoa code?  Thanks

----
Works here in a quick shell test, so it should work fine in an NSTask too. Maybe you're passing the argument wrong. PostYourCode.

