---
layout: page
title: HowToBuildUniversalLibraries
---




There is a page LinkingAndUniversalBinaries under ApplicationLinkingIssues that outlines what happens if you have not, in fact, built universal binaries of the libraries for your project

----

I've been getting my project ready to be compiled as a Universal binary, but I've hit a snag: my project uses several static libraries, which are for the moment PPC only, and I would need to have universal versions of these to properly build a universal app.

Are there any pointers available on how to build universal libraries (the .a and .dylib kind)?

*That's a pretty general question, and the answer isn't really much different from how you build a universal application. Is there a particular step in the process that you're having trouble with?*

I'm trying to build an open-source library (I'll leave it at that, since being more specific generally won't help future readers, and this is likely to be a 'common problem' for general OS code anyways), so it's the kind that you do './configure, make, make install'; I've tried inserting the custom environment variables between the     .configure and     make commands as described at http://developer.apple.com/documentation/Porting/Conceptual/PortingUnix/compiling/chapter_4_section_3.html but all I ever get is only ppc binaries. I'm guessing maybe further changes are needed in the makefile, but I haven't a clue on what to look for or do (I usually just use libraries, not compile them).

----

Check out this article: http://developer.apple.com/technotes/tn2005/tn2137.html

----

when I try it on the code I'm trying to compile, configure croaks with those settings:
    
...
checking for gcc... gcc
checking for C compiler default output file name... configure: error: C compiler cannot create executables
See `config.log' for more details.


and the relevant portion of the log:
    
...
configure:2072: checking for C compiler default output file name
configure:2075: gcc -isysroot /Developer/SDKs/MacOSX10.4u.sdk -arch i386 -arch ppc  -Wl,-syslibroot,/De
veloper/SDKs/MacOSX10.4u.sdk conftest.c  >&5
/usr/bin/ld: -syslibroot: multiply specified
collect2: ld returned 1 exit status
/usr/bin/ld: -syslibroot: multiply specified
collect2: ld returned 1 exit status
lipo: can't open input file: /var/tmp//ccpHzug6.out (No such file or directory)
configure:2078: $? = 1
configure: failed program was:
...


a search for this "-syslibroot: multiply specified" error yields that this seems to be a common problem, with no clear solution that I have yet seen.

----

From http://gcc.gnu.org/ml/gcc-patches/2005-06/msg00308.html,
it looks like the     -syslibroot stuff is no longer necessary.  A project built just fine using this:

    
env CFLAGS="-isysroot /Developer/SDKs/MacOSX10.4u.sdk -arch i386 -arch ppc" ./configure --disable-dependency-tracking


----

Similar to the above code, but if you're wanting to compile from the command line directly, you can use this:
    
gcc myapp.c -o myapp -arch i386 -arch ppc -isysroot /Developer/SDKs/MacOSX10.4u.sdk


----
I was getting tons of "cputype ppc does not match cputype arch i386" general errors, after following Apple's directions to a T.  As it turns out, you have to delete and re-import all the frameworks you use in the app after changing the build settings, and make sure you bring in the frameworks from the 10.4u(niversal binary) SDK in the /Developer folder, and not the standard /System frameworks.  If you get any X "expected to be defined by Cocoa" errors, this is the same problem.  Otherwise, the Apple instructions are complete, as much as they frustratingly aren't.
-Stephen

----
I'm not sure what kind of problem you ran into (Xcode is buggy so often) but you shouldn't have to do what you did. Adding frameworks directly from /System is fine, and keeping your old /System frameworks is also fine. What setting the SDK does is add an implicit search path to all such references, so that it will look in the 10.4u's /System before looking in the root one.

