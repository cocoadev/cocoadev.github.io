---
layout: page
title: LinkerWarningsAndHowToKillThem
---

Hullo. My project consists of two targets, an application and a framework target which is used in the application. At the moment I'm focusing on getting the framework target working properly.

It compiles perfectly reasonably, including my nice prefix header and all the other goodness I have set up, but when it gets to linking it pops up a strange warning I've not seen before:

    ld: warning dynamic shared library: /usr/lib/libSystem.dylib not made a weak library in output with MACOSX_DEPLOYMENT_TARGET environment variable set to: 10.1

Does anybody know what this is indicative of? Is it harmful? How do I squash it?

And as an auxiliary point, does anybody else have strange linker warnings they're battling or have already defeated? It might be nice to get up a brief list of them that we could refactor into a LinkerWarnings page or something.

Thanks,

-- RobRix

Whoops, I had the     -seg1addr 0x10000000 bit in compiler flags rather than linker flags. That's now taken care of. Of course, now I'm getting five warnings instead of just one:

    
ld: warning prebinding disabled because (__TEXT segment (address = 0x10000000 size = 0x2000) 
        of /Users/iapole/Developer/Projects/Firestorm/build/Fireform.framework/Versions/A/Fireform 
        overlaps with __TEXT segment (address = 0x10000000 size = 0x9000) 
        of @executable_path/../Frameworks/Lodestone-Aegis.framework/Versions/A/Lodestone-Aegis
ld: warning prebinding disabled because (__DATA segment (address = 0x10002000 size = 0x1000) 
        of /Users/iapole/Developer/Projects/Firestorm/build/Fireform.framework/Versions/A/Fireform 
        overlaps with __TEXT segment (address = 0x10000000 size = 0x9000) 
        of @executable_path/../Frameworks/Lodestone-Aegis.framework/Versions/A/Lodestone-Aegis
ld: warning prebinding disabled because (__OBJC segment (address = 0x10003000 size = 0x1000) 
        of /Users/iapole/Developer/Projects/Firestorm/build/Fireform.framework/Versions/A/Fireform 
        overlaps with __TEXT segment (address = 0x10000000 size = 0x9000) 
        of @executable_path/../Frameworks/Lodestone-Aegis.framework/Versions/A/Lodestone-Aegis
ld: warning prebinding disabled because (__LINKEDIT segment (address = 0x10004000 size = 0x1000) 
        of /Users/iapole/Developer/Projects/Firestorm/build/Fireform.framework/Versions/A/Fireform 
        overlaps with __TEXT segment (address = 0x10000000 size = 0x9000) 
        of @executable_path/../Frameworks/Lodestone-Aegis.framework/Versions/A/Lodestone-Aegis
ld: warning dynamic shared library: /usr/lib/libSystem.dylib not made a weak library in output 
        with MACOSX_DEPLOYMENT_TARGET environment variable set to: 10.1


Alright, so this basically makes sense. I have to set the address to something else because I'm already loading in one framework (Lodestone-Aegis) from the bundle. The question is, what do I set it to? 0x10009000? I am completely lost here.

Thanks,

-- RobRix

----
For your original question (and the 5th of your warnings), there is a bug along these lines discussed at http://developer.apple.com/qa/qa2001/qa1233.html . I followed their 2nd corrective option and made a (unused) function that calls malloc so the compiler would link the library strongly. This eliminated your last "not made a weak library" warning for me.

**Thanks, that worked beautifully. As for the other four, I just stopped importing the code as a framework and instead compile it directly into the Fireform target. Still, I'd like to know what exactly I should have been doing to get it to work.

As a bonus question for those inquiring minds out there, if I have an application which has two bundled frameworks, one of which links against the other, will it work? Or will I need to duplicate the second framework within the first?**

----

If you don't already know this, Apple has reserved addresses above 0x40000000 (that's 7 zeros). I posted a discussion once trying to find if there was a way you could tell what the range of addresses that a particular framework uses (AddressRangePrebindingFrameworks) but nobody responded, so I still don't know how to do this. The reason that I say this is because the only way you can get prebinding to work is to make sure that none of you frameworks clash with each other, but the only way to do this is to know the range of addresses each framework uses. I think it's safe to say that AppKit and Foundation are above 0x40000000, but I don't know if OpenGL is (it should be).  

It looks like the address range for Lodestone-Aegis is clashing with the address range used for Fireform.     seg1addr specifies the base virtual memory address for each framework. If you are using     -seg1addr 0x10000000 for both frameworks then this is your culprit. You should assign a different starting point for each framework with the hopes that you have spaced your frameworks enough to avoid overlapping. But how much you should space your individual frameworks is trial and error (until someone can enlighten us all as to how you can figure out the required address range of a particular framework). If you are using third party frameworks and if you don�t have access to the source to rebuild the frameworks of these third party frameworks, then you have to hope that one third party framework�s address space doesn�t clash with another third part framework�s address space. 

Mach files assign addresses for functions, data references and constants, so the range of addresses that a particular framework uses will change if you add methods, functions and instance variables. One trick to know about Mach files is that you can build against an old version of a framework and have you app link against a new version of the same framework at runtime without having to recompile your app if no new classes and/or instance variables were added to the framework. This is useful info if you want to update a shared framework without having to recompile all dependent apps. Another tip is to always use a generic instance variable of type �id� for every class you create. Use this generic instance variable to reference a mutable dictionary so you can modify classes without adding instance variables while you are prototyping a framework. This speeds up your turnaround time immensely (I�m sure XCode will make this a moot point). This also gives you more flexibility to update deployed frameworks. 

Here�s a link to some useful info http://developer.apple.com/documentation/DeveloperTools/Conceptual/MachORuntime/2rt_mach-o_overview/chapter_2_section_3.html

--zootbobbalu

