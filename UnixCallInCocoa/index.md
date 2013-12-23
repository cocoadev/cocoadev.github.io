---
layout: page
title: UnixCallInCocoa
---



See main General/NSTask page for introduction to General/NSTask method     launch and example of use

On using the     system( ) or     open( ) calls:

I would like to know how to hook a Unix call from a Cocoa application. Is there any workaround to do or just call it as a plain C line ?

----

Do you mean a system call? like open() or fork()?

----

Assuming I am interpreting your definition of *hook a Unix call* correctly, Yes, Just include the relevant header file and call it, just as in plain C. Depending on the function, you may need to make the linker aware of the library it resides in, so that it can resolve it.

Note, Hooking function entry points can mean something very different than just calling them.

 -- General/NeilVanNote


----

If you mean to run a unix app from within Cocoa  (such as 'ls -a') you can use in your code the following:
system("ls -a");

Cable

----

General/NSTask and General/NSPipe give you more control over the process, however.
