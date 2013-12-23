---
layout: page
title: SIGBUSError
---

I'm working on an OpenGL-cocoa project, and as soon as I add new code, I get a SIGBUS 10 error. If I move around the code and switch its positions, I might sometimes get it to work. Not this time though.  I allways get a SIGBUS error!

I have a method, that loads data from a file and makes an GL_COMPILE list of it. The problem seems to be in this method, since if I don't call it in my app, I don't get any SIGBUS-error!  But why? There are no syntax errors in the method and when I run the app, the method completes successfully (I see things in the window just before the app terminates).  By the way, it seems to terminate just on the moment when it's supposed to call the applicationWillFinishLaunching: selector, but I don't know really.

Can anyone PLEASE help me out?  This is really important!



Thanks major in advance!

--AntonKiland

My guess is you are getting a memory access error. SIGBUS 10 error is usually due to an attemp to write to a memory address that hasn't been allocated. Sounds like some class is expecting an object to exsist that hasn't been allocated and initialized yet. It's hard to say without seeing the actual code. 

A good practice to get into is to set all instance variable pointers to nil when an object is initialized. Don't assume that all variable declarations will automatically be set to nil (zero). Maybe someone can enlighten all of us why this has to be, because I would say that 99 percent of the time variable declarations are automatically set to nil. But, every once and a while I have seen newly declared variables initially set to some value other than nil (zero). 

----

When you alloc an object, it will be cleared to zero (it's documented in NSObject).  If your crash changes when you move code around, you're most likely stomping on a random piece of memory somewhere, and then someone else is using that address and the getting the addressing error. Take a good look at your memory usage, that you're allocate sufficient memory for stuff (remember any hidden memory you have to account for, like the trailing zero byte in C strings).  MacEdition has a thing on using the malloc tools for tracking down some memory corruption stuff: http://macedition.com/bolts/bolts_20021210.php . ++MarkDalrymple

