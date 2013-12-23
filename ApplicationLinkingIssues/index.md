---
layout: page
title: ApplicationLinkingIssues
---




*
This page discusses several examples of various specialized linking issues encountered by Cocoa developers. It may provide insight if you are having linking difficulties with auxiliary libraries and frameworks, etc.
*

For an overview of the linking process, see ApplicationLinking.

[Topic]

ZeroLink is an important feature of Xcode development that may manifest itself when you are trying to deploy your application
away from your development system.

----

**Linking against a static C library**

I have a framework that I am linking against a static C library. The framework compiles fine, but when I try to link that framework into an application to use it, I get the following compiler error:
    
ld: /Library/Frameworks/PostgreSQL.framework/PostgreSQL
is input for the dynamic link editor, is not relocatable by the static link editor again


I've never seen this error before. Can someone help me fix it? I have been struggling with it all day.


Thanks for any help you can give,

-- EliotSimcoe

The result of this linking operation would be a static library. But one of the source files (the framework) is already
designated to be a dynamic library. Since it is not possible to change a dynamic library into a static one, you
get a linker error.

The solution should be not to link your library against the framework at all. The symbols in your library that reference the framework will stay unresolved, so your program (which uses the static library) will have to link
against the framework.

This is by design: consider a static library with two functions, load_jpg and load_png. The first one needs libjpeg, the second one libpng. You now link your program against this static library, using only load_jpg. Since load_png is referenced nowhere in your program, the linker would strip it out, at the same time removing all dependencies on libpng (resulting in a smaller executable and less dependencies).
-- JohannesFortmann

----

**@executable_path/... image not found**

Sometimes you might get a runtime error like this:

    
[Session started at 2006-02-11 08:56:32 +0200.]
dyld: Library not loaded: @executable_path/../Frameworks/OgreKit.framework/Versions/A/OgreKit
  Referenced from: /Users/peti/Programming/pjetexst/build/Debug/pjetexst.app/Contents/MacOS/pjetexst
  Reason: image not found

pjetexst has exited due to signal 5 (SIGTRAP).


This means you forgot to create a Copy Files build phase for the library in question. This error signifies that the framework is meant to be embedded in your .app. In this case, simply linking against it is not enough, you have to make sure that it really does get bundled in your .app. The Copy Files phase will ensure this. To do this, drag the specific framework icon from the framework section into the Copy Files icon under your specific target and rebuild.

--
I added "A Copy Files build phase" and the files are indeed added to the app's bundle ("show package contents"), but I still get "excited due to signal 5". All help is appreciated. My sanity will thank you. 


----

**Linking against: libmx.dylib**

The physics engine I'm using links against libmx.dylib, so I have access to single-precision floating point math. believe it or not, it actually makes a difference, since the engine runs a fair bit faster in single precision mode and having to convert single to double and from double to single with every math function, there's a noticeable overhead.

Now, as far as I can tell, this lib isn't available in Jaguar ( it is in Pather ).

So what I'm wondering, is this -- how can I statically link against it? And if I could, would it help/work? I looked in /usr/lib where libmx.dylib lives, and I don't see anything like libmx.a

Any ideas?

--ShamylZakariya

Source is available, so you should be able build a static lib. Check the darwin sources, I saw libmx come up more than once in the libm build project. It is probably built from the same source with different flags.

http://cvs.opendarwin.org/index.cgi/src/Libm/

Or somewhere buried in

http://www.opensource.apple.com/darwinsource

----

**Linking issues associated with -lmp**

I'm having trouble with my project.  All the code is fine, and it compiles fine, but Xcode gives this error when linking:

    
Building target �skiTunes� � (1 error)
can't locate file for -lmp
Command /usr/bin/gcc-4.0 failed with exit code 1


What's wrong? Any help is appreciated -

If you want to take a look at the code, you can get it via Subversion by putting this into the Terminal and hitting return:

    
svn co http://opensvn.csie.org/SkiTunes/skitunes/trunk/ skitunes


Thanks!
JohnWells

This means that XCode is looking for a library called libmp.dylib or libmp.a, and not finding it. There are two reasons that it would be looking for a library: either you told it to, by adding it to the project or to by passing -lmp as an option to gcc; or the compiler generates a dependency. In the latter case, if it's a compiler-generated dependency to a library that isn't there, and you've got a full install of developer tools, it's an Apple bug.

----

**Library naming conventions 101**

Disclosure up front: This is not strictly a Cocoa issue, but a GCC issue.

I have a Cocoa project that links with a C++ library that is located at ~/Desktop/XYZ/xyz.a

Its headers are in ~/Desktop/XYZ/include

I specify the path to the headers under the Build tab of the target inspector (or whatever you call it) by fully expanding the above path

I specify the path to the library by fully expanding its path

When I build the Development target the build is successful

However, when I build the Deployment target the build log gives me the following message and the build fails.

*Well, duh. There was a big hole in my understanding of linking. The library has to be named libxyz.a.*

*Other holes, yet undiscovered, remain. However, none of them are causing me problems in this project.*

----

How can I determine where a linking imperative is coming from?  I recently upgraded to Leopard and now a project of mine is failing at the end of compilation in the linking phase, with one error:

    
    /Developer/usr/bin/gcc-4.0 -o "/Users/stephen/Documents/Programming/SunDog/VIDEO EFFORT/VL/build/Debug/Clips.app/Contents/MacOS/Clips" "-L/Users/stephen/Documents/Programming/SunDog/VIDEO EFFORT/VL/build/Debug" "-F/Users/stephen/Documents/Programming/SunDog/VIDEO EFFORT/VL/build/Debug" -F/Developer/SDKs/MacOSX10.4u.sdk/System/Library/Frameworks -filelist "/Users/stephen/Documents/Programming/SunDog/VIDEO EFFORT/VL/build/VL.build/Debug/Clips.build/Objects-normal/ppc/Clips.LinkFileList" -framework Cocoa -framework QTKit -arch ppc
ld: file not found: /System/Library/Frameworks/CoreServices.framework/Versions/A/Frameworks/WebServicesCore.framework/Versions/A/WebServicesCore
collect2: ld returned 1 exit status
	ld: file not found: /System/Library/Frameworks/CoreServices.framework/Versions/A/Frameworks/WebServicesCore.framework/Versions/A/WebServicesCore
	collect2: ld returned 1 exit status


So it looks like 2 things are happening here: 1) some kind of "LinkFileList" is being generated.  Where can I see the complete list and maybe edit it?  2) it seems like the list consists of "-framework Cocoa -framework QTKit -arch ppc".  When I check out frameworks, they all point to good, 10.5 locations.  Nothing suggests to me that it's looking for WebServicesCore.framework.  How do I find out who is asking for this file so I can alter the offender?
----
I'm having the same linking problem on a target recently moved from 10.4 to 10.5 targeting.  Nothing else in the project has changed, I've nuked the build directory, even deleted and re-established links with all the frameworks I'm using just to make sure I wasn't dragging something old in.  Stumped.
----
**SOLVED!**
ld: file not found: /System/Library/Frameworks/CoreServices.framework/Versions/A/Frameworks/WebServicesCore.framework/Versions/A/WebServicesCore
	collect2: ld returned 1 exit status

Remove the 10.4 search path from the project.pbxproj in the FRAMEWORK_SEARCH_PATHS entry in the buildSettings.  Look for the section beginning with "/* Begin XCBuildConfiguration section */
"

----
Does anyone know the XCode or Apple equivalent of the windows Dependecy Walker tool? I looked at otool for a while, but it didn't seem to have the options I wanted for finding dependencies.

otool -L will show those dependencies.

