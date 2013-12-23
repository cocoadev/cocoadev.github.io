---
layout: page
title: APIs
---



Plural of API, Application Programming Interface. To my knowledge frameworks don't fall into this *per se* since API refers more to, for instance, the headers you'd write for plugin developers for your app. So FoundationKit, CoreFoundation, et cetera aren't really APIs, are they? -- RobRix

Well, I've often heard Cocoa as a whole referred to as an API. It's the interface to the OS, after all. I'd say AppKit and FoundationKit are APIs, yes. -- KritTer

Debatable, since the OS isn't AnApplication. But it's only nitpicking in any case. -- RobRix

An OS is The Application imho -- peacha

According to The Free On-line Dictionary of Computing, 1993-2003 Denis Howe:

*(API, or "application programming interface") The interface (calling conventions) by which an application program accesses operating system and other services. An API is defined at source code level and provides a level of abstraction between the application and the kernel (or other privileged utilities) to ensure the portability of the code.

An API can also provide an interface between a high level language and lower level utilities and services which were written without consideration for the calling conventions supported by compiled languages. In this case, the API's main task may be the translation of parameter lists from one format to another and the interpretation of call-by-value and call-by-reference arguments in one or both directions.*

----

An API encapsulates functionality in a fixed way. For instance, you know that you can write this code:     [NSObject alloc] and get a reference to an NSObject object. It's understood that this will always be the way to allocate an object and return a reference to it, so it's part of the API for managing objects. Certainly, +alloc can and probably will do things you don't want or need to know in order to instantiate the object (like, for instance, call +allocWithZone:, which in turn will call one of those objc_ runtime functions), which is what makes it an interface.

Note that in ObjectiveC, you define objects as @interface and @implementation. Every class you define is a sort of mini-API for the functionality of that class, since its interface can be used by other code.
-----

It should be noted that Apple marketing uses the initialism to mean "interface". For instance, when the release Xcode 4, they talked about it having 1500 more APIs. I, for one, hate this use of the term.

I suppose what they are counting is every object method, including getters and setters, and every function. I would tend to say that the entire @interface section is an API, and a collection of related functions (Like the various CG*Make functions) would be a single API, but clearly the definition is mushy.

Wikipedia puts the dividing line for an [http://en.wikipedia.org/wiki/Application_programming_interface API] at the library level, which really matches my idea pretty well.

So, let's posit that

***Framework** is a collection of libraries
***Libraries** are a collection of related classes and/or functions
***APIs** are the interfaces those libraries present to the **programmer** and are mediated by compilers
***ABIs** are the interfaces those libraries present to the **programs** and are mediated by linkers.

Does this make sense to people?

Also, perhaps all this ought to be moved to the Discussion page?
User:Hacksaw|Hacksaw (User talk:Hacksaw|talk) 05:57, 1 March 2013 (EST)

