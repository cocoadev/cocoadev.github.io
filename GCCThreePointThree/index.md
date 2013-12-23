---
layout: page
title: GCCThreePointThree
---

I just downloaded and installed Dec2002gccUpdater.pkg. I got into the habit of declaring variables all over the place, and this update doesn't set the default to allow this. Does anyone know what the setting is to allow you to declare variables anywhere?

----

I don't see why this wouldn't work. What are the errors you're getting? -- AdamAtlas

Oh, now I see, it only lets you declare variables at the beginning of a function/method. Hmm. Anyone know how to change this? -- AdamAtlas

----

I remember I had this problem with 2.95 and then it went away in 3.1, but I just switched a project over to 3.3 and I don't have this problem.  Are you using the command line or project builder?  Anyway, I didn't see any switch covering this but I believe the ability to do this was added in the most recent ISO C standard so maybe the -std= switch can be of help.  -- Bo

----

Is there any specific reason to upgrade to GCC 3.3 before the release of Panther? I am slightly wary up upgrading for fear of it messing up my current projects. Can anybody quell my fears (meaning, has somebody upgraded and found no problems with compiling previous projects, and what improvments if any there are?). Also, does anyone know how to get a copy of the prerelease Xcode? It doesn't seem very fair of Apple to only give copies to the people who could afford the thousands of dollars in admission fees and airfares to attend WWDC, and then leave the rest of the developers in the dark. 

-- MarcWeil 

----

I couldn't wait, so I restored my orignal dev setup. I'll wait and see if this is going to be a problem. I did notice that builds took much longer, so maybe there are problems with this update. 

And I agree with Marc about this being lame that Apple isn't let everyone at xCode!!!!

lol, yeah! me too!

----

My experience:

It is *much* slower -- a build that previously took 17 minutes (with 3.1) now takes more than an hour (but probably this is related to precompiled headers, which I haven't found any info about with 3.3, other than the system has changed).

I only got 2 warnings with a rather large project, my usage of the 'offsetof' macro (which I think is illegal for other than POD, so it was probably correct, and the usage of "std::vector<NSMutableDictionary *>", this seems to be a bug in gcc 3.3, but I wrote this wrapper:

    

   template <typename _ClassT>
   struct ObjCWrapper
   {
      _ClassT *Instance;
      ObjCWrapper (_ClassT *instance) : Instance(instance)  { [Instance retain]; }
      ObjCWrapper (const ObjCWrapper &o)                    { Instance = [o.instance() retain]; }
      ObjCWrapper& operator= (const ObjCWrapper &o)         { Instance = [o.instance() retain]; }
      ~ObjCWrapper ()                                       { [Instance release]; }
      operator _ClassT * () const                           { return Instance; }
      _ClassT *instance () const                            { return Instance; }
   };



And yes, now it seems that the bug with calling ObjectiveC methods in template code is fixed. There was also another rather serious bug haunting one of my projects (causing memory trashing) and this bug is gone in 3.3, but it is still present in the updated 3.1 which was included.

So all in all... it's much slower, it has a few fixes and at least one new bug -- perhaps it will be faster when I figure out how to use precompiled headers.

----

Alright, thanks for the heads up then. I guess I'm sticking with GCC 3.1 until Xcode is released with Panther. I'm glad I didn't apply the upgrade, as I haven't come across any bugs in 3.1 yet, and I *really* don't want slower build times.

Also, can someone from WWDC who has a copy of Xcode tell us how it is as of now? It would at least be nice to know how good of a beta this is, and what it is like to program in an interface modeled after iTunes. ;-)

-- MarcWeil

----

Well, 3.1 is also included with the update, so you can still use 3.1 after the update. For code size, 3.3 seems to be worse than 3.1 (which again was worse than 2.96). My executable grew from 7-800 kB to 930 kB...

Important: the updated 3.1 comes with no wchar_t (so it would seem), so I have dependant code (Boost's regex++ library) which no longer can compile!

----

To answer the original question, declaring variables anywhere in code is not part of ObjC, but of C++. To turn it on, you could use the .mm prefix on your files - but be aware this makes compiles take much longer! OTOH, it provides other benefits, like the ability to add even more ObjectOrientedDesign to your code without runtime cost.

----

I believe this restriction was lifted in ISO C 99, but when I moved to another project, I found that the default compiler had somewhere been set to the 2.95.2 compiler and I was getting the exact same errors.  Try running     sudo gcc_select 3 in the Terminal, and see if these issues go away. -- Bo

----

C99 (which is ANSI, not ISO IIRC *actually, both, it's ANSI/ISO C99*) does allow for placement of variables declarations elsewhere than in the beginning of a scope. But C99 compliance was first meet with GCC 3.1. The gcc_select is only for the cc and c++ symbolic links (when is used when launched from the terminal, e.g. makefiles), normally you select the compiler to use in the compiler settings (of ProjectBuilder) on a pr. project/target basis.

----

Not quite.  gcc_select also sets what the default compiler is in ProjectBuilder, but, interestingly, if you gcc_select 3.3, it sets ProjectBuilder's default compiler to 2.95.2.  ProjectBuilder also seems to become confused by this discrepancy and will use 2.95.2 regardless of what the compiler popup says.  You can set CC = /usr/bin/gcc3 in the expert build settings for each target if you're feeling industrious or you can just run gcc_select 3 to fix this globally.  -- Bo

----

Another thing to be aware of is, that after installing this update it will set the default compiler and thus includes to 3.3, so even though my targets use 3.1, they will use the 3.3 includes.

And btw: I was the one who stated that my code grew a few hundred kilobytes with 3.3, this was a premature statement, I had overlooked an "include <iostream>" which was responsible (it has the same effect with 3.1) -- speaking of which, why the f*** does #include <iostream> add ~300 kB to my executable (even if I don't use *any* functions from the header), #include <string.h> has a similar effect... this is really absurd, because my executable is almost a megabyte, but my own code would compile to ~300 kB, and this is actually far to much IMHO... I am really not impressed by gcc... one of these days I just have to start my own compiler project, for no other reason than to find out that it *has* to take 17 minutes to compile 14.000 lines of code on my GeeFour and the executable just has to be almost a megabyte in size... NOT! :-)

----

The problem is that the Mac OS X linker doesn't strip dead code, unlike, say, CodeWarrior, MPW, or Think C circa 1991...

**Doing a clean build should take care of this, shouldn't it?**

No, doing a clean build will remove the object files which are no longer in use (i.e. if you renamed a file), but dead code is still added to your executable.

**Ah, okay, my mistake.**

----

I'm not sure about this, so I hope someone with more GCC experience can help, but I think "string.h" is already included in Foundation Framework because I use strstr(), strcmp() and strncmp() all the time without including it. I also wonder if you are supposed to import all headers with Objective C (along with C headers like fcntl.h) because you CAN import "fcntl.h" when you need O_RDWR, O_RDONLY and O_WRONLY declared. --zootbobbalu


----

It is a good habit to always include the headers that define what you will be using, and not to rely on somebody else to include them somewhere along the way.  For standard library functions, the manpage will tell you what you should include.

I upgraded to gcc 3.3 with very few problems.  Mind you, this was for a million-line C++ project, with probably only 100 lines or so of  Objective-C.

The best way to "force" the 3.3 compiler for all targets in a project, IMHO, is to modify the "Build Styles" for the project.  Under the Targets tab, click on the Development or Deployment build style (depending on what you're building), and add the following settings:

     
   CC  = /usr/bin/gcc-3.3
   CPLUSPLUS = /usr/bin/g++-3.3
   GCC_VERSION = 3.x


If you have multiple targets in your projects (as I do) this is WAYYY easier than going to the "expert build settings" for each target.

   -dweebert

