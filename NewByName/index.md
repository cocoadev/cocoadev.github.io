---
layout: page
title: NewByName
---

Sorry if this is answered elsewhere - I'm sure I have seen it somewhere but search didn't dig it up...

How can I make an object of class <foo> if I have a string containing its classname? e.g. if I have @"NSControl", how can I get a new instance of an NSControl?

----
[NSClassFromString(@"NSControl") alloc]

----

If it's a C-string: [objc_getClass("NSControl") alloc]

