---
layout: page
title: StaticVsDynamicLinking
---



*Very important article...must read!*

The project I'm working on is getting to the point that I'd like to be able to distribute it... and the notes regarding embedding frameworks I've read on this Wiki have helped a lot, since I've factored my project into quite a bit of logically separate frameworks. Using     otool I can see that the embedded versions are in fact being linked against (which makes me happy ;) 

However, my program requires the dlcompat library so that I can use     dlopen()/dlclose().... I installed     dlcompat-* via fink, such that I have /sw/lib/    libdl.dylib &     /sw/lib/libdl.a as well as necessary headers (dlfcn.h).

Anyway, I intentionally link against     libdl.a in my program such that the code is built into the executable instead of dynamic linking, since I don't want people to have to install fink and whatnot. However, looking at the build process, it seems that g++ is invoked referencing     libdl.a as, simply,     -ldl, which makes sense. However, that means that gcc will preferentially link against     libdl.dylib, which is the opposite of what I want.

I would just uninstall the shared-lib version of     dlcompat, but I need it for other reasons (some technical X11 programs I can't live without).

So, does anybody know how to make gcc preferentially use a static lib when a dynamic version is present? This is the last step for my program to become self-contained and runnable on any OS X machine.

-- General/ShamylZakariya

Never mind! I downloaded the source via fink and built it in, directly, into the executable. Not the best solution, but it works ;)

-- General/ShamylZakariya

I believe one can include .a libraries in a PB Project, which causes them to automatically statically link.  That would be one solution.  --General/OwenAnderson

That's what I did, actually. but if you look at the build process you'll see it still calls -l<libname>, instead of forcing an explicit linkage. --General/ShamylZakariya

I don't know then.  I did that once with a library that I didn't have the shared version installed, and I know that it worked properly.  Your mileage may vary.  --General/OwenAnderson


----

I'm confused about these dynamic linked libraries vs. statically linked, I'm not quite sure what the difference is, how they are made and why anyone would want to make static instead of dynamic, and so forth. Could someone explain? I've tried to google for a tutorial or explanation, but no luck... --General/TheoHultberg/Iconara

Generally you'd want to use dynamic libs since dynamic libraries are shared by any number of apps. It saves disk space and has the advantage that if it's updated (or bug fixed) all apps using it benefit. That's why Cocoa and Foundation and other system libs are dynamically linked -- when apple releases an update or bug fix we all benefit. However, sometimes you need a particular version of a lib (perhaps a bug fix broke a behavioural dependancy of yours) or (as was my case) you need a library which other folks are unlikely to have. That's where static libraries come in -- they're compiled directly into the application executable. It makes the app bigger (though only as big as all the required methods come to: if you've got a huge lib of which you only use 3 methods, your app will only get three method code blocks bigger ).

It's easy enough to package a framework (which is a kind of dynamic library) in your app bundle, but I'm uncertain how to package a traditional .so library in the app bundle. Anyway, the lib I needed was something General/MacOSX users can't get without using Fink to install it. That's an unreasonable requirement! So I built it into my app. 

In retrospect, I should have used my mediocre gcc understanding to add parameters in the build settings to force an explicit linkage. Oh well.

--General/ShamylZakariya

And how would you have done that???? How do you statically link a dylib library into your executable. I need to be able to do this to make my program portable as well.

Unless you have the source to the dylib -- so you can compile a standard library ( .a ) and then make gcc link against that -- I think you're SOL. Sorry. I'll bet there's some way to get automatic linkage to .dylibs packaged in the app bundle, but I have no idea how. Anyway, when you're mucking with this `otool -L` is your friend.

Your worst case situation is to have to dlopen a dylib in your General/MyApp.app/Contents/Resources folder and manually resolve a function ptr using dlsym. The irony, here, is that it's dlopen/dlsym/etc that I was writing about above, since by default OSX doesn't support it. A little chicken and egg, here :/
--General/ShamylZakariya

----

**gcc linker hates static linking** -- see some of the pitfalls in the following....

So, I recently added nice truetype text overlays to my opengl simulation, using the FTGL library. Works as advertised, looks sweet, no problems.

Since I want my program to be a normal drag-n-drop install, all requisite frameworks are packaged in Contents/Frameworks, and anything which doesn't work well as a framework is compiled to a static library and linked directly into the app executable.

For my app, this means my own core frameworks are packaged in the frameworks folder, and 3rd party libraries, like the Open Dynamics Engine, FTGL and its dependent freetype are compiled as static libs. So I have libode.a, libftgl.a and libfreetype.a

However, to build FTGL I had to do a make install on freetype, so I have freetype installed in /usr/local/lib -- which means libfreetype.dylib is installed alongside libfreetype.a

The trouble is this: I want Xcode to use libfreetype.a, but it wants to be "smart" ( or more likely GCC does ) and it  uses libfreetype.dylib instead. This means my app won't work on any machine without freetype installed.

So, how do I get GCC to use static linking against libfreetype.a? is there any linker flag to force static linkage? Anybody tackled this before and figured it out?

--General/ShamylZakariya

*Apple's ld absolutely hates static linking. It will always take the dynamic library if both are present, no matter what you tell it. The solution is then to make sure the dynamic library is not present, by either renaming the dylib (temporarily, if need be) or renaming the .a.*

----

I gave it a shot by eliminating the dylibs, and libftgl.a compiled and linked fine. However, when I tried to rebuild my app agains the new libs, I got this linker error:

    
ld: lib//libfreetype.a(ftgzip.o) illegal reference to symbol: _inflateEnd defined in indirectly referenced dynamic library /usr/lib/libz.1.dylib


Now, this is odd. Why should this have linked fine, when libfreetype was dynamically linked, but not now?

----

Since dynamic libraries are, well, dynamic, they have their own linker support. If you link against libFoo and libFoo links against libBar, dyld will load libBar when it loads libFoo. Static libraries are handled at compile time, so if you have a static library that uses libBar, your app must link against libBar itself in order to get dyld to load it when your app is run. Just link your app against libz and all will be well.

----

You're right. The thing is, I already *had* /usr/lib/libz.dylib in my project as a lib to link against, but it took the addition of "-lz" to the linker flags to get it to actually do it. Seems odd, since generally adding any lib/dylib to a project will automagically result in a -l<libname> to the linker flags, but, hey. What can you do.

The important part is that now, it works ;)

--General/ShamylZakariya
