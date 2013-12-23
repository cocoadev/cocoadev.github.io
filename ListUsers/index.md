---
layout: page
title: ListUsers
---

What is the best way to get a listing of all users known on a system (even if they are only local domain ones, that is still useful)?  Is this functionality exposed within Cocoa or should I look to the lower-level APIs?

----

It's not part of Cocoa. Look at the man page for     niutil

----

You may also want to give     getpwent a try.     man 3 getpwent

*Will this work on OSX? I.e. is it NetInfo aware?*

----

After further investigation, it appears as the the DirectoryService framework is the way to do this, correctly.

It is a pretty ugly API but Apple's sample code is pretty useful.

