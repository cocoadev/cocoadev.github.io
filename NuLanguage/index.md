---
layout: page
title: NuLanguage
---

Nu is a Lisp-like programming language meant to be a higher-level interface to Cocoa and Objective-C. It is not yet available to the public, but a blog post describing it is located here: http://blog.neontology.com/posts/2007/08/11/whats-nu

Nu has a Lisp-like syntax, a SmallTalk-like object model, and sits directly on top of the Objective-C runtime. Due to this design, there is no language "bridge", as such, but rather any classes written in Objective-C are directly visible in Nu and vice versa. Basic Cocoa types such as NSString and NSNumber are used directly in Nu, rather than having to be converted during the bridging process.

----

I'm doing something similar with a language called Locus for a program I'm writing; ObjC implementation, directly using Cocoa classes where applicable, etc. I don't have a public release, but Locus will probably be open sourced when I'm done. Do you think there's any interest in talking about how we've built this sort of a system? I noticed that the Lua stuff on here got a positive reaction... -- RobRix

----
I'd be interested to hear, anyway. A little variety wouldn't hurt this site at all. -- MikeAsh

