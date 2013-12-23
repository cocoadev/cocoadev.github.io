---
layout: page
title: UniversalBinariesAndJaguar
---



Will a universal binary run under Jaguar? I don't have Jaguar here to test, but I believe you need to use GCC 4.0 to compile a universal binary, and GCC uses some libraries (possibly /usr/lib/libmx.A.dylib) that aren't available on Jaguar. If this is right, then either you compile for universal binaries for the future, or you compile under GCC 3.3 for the past. Is this correct? Or is there a way to compile a universal binary that will run on Jaguar?

----

It's possible to construct a universal binary using the     lipo tool from any pair of binaries. The universal binary mechanism is present back to early beta versions of Mac OS X, and so as long as your PPC version works on Jaguar, the universal binary will too.

----
"Everyone can relax. It is possible to build the General/PowerPC version separately with gcc3.3 and the Intel version on gcc4.0 and merge them into one universal binary. We've been researching this topic exactly here at WWDC. "
Found this at Camino forums, topic:
 http://forums.mozillazine.org/viewtopic.php?t=276291&sid=7f605343cdf8808804995b63d21f6312

General/EnglaBenny

----

So do we have a specific method for doing this? I didn't see it on there. Hopefully some documentation will come soon.

----

As a previous person mentioned, you can use     lipo to combine two binaries. This is exactly what Xcode 2.1 does for you behind the scenes. But if you need to do it by hand, here is how:

    /usr/bin/lipo -create "ppc-binary" "x86-binary" -output "universal-binary"

Check out     man lipo for more information. -- General/TimothyHatcher

*Hmmm, so you could use that to merge 64-bit G5 code and older G4 code too? Too bad you can't use it to make OS-specific binaries... --General/JediKnil* 

Jedi: Fat binaries can be used to create mixed ppc/ppc64 executables without any problem. You can do so by adding ppc64 to the Architectures section in Xcode (exactly the same way you add i386 to create x86 binaries).

**That's what I was going to write at first too, but he did say General/OSes - maybe different code for 10.2, 10.3 etc.**

There's not as much need, since the same effect can be had with a single binary and multiple bundles that are loaded by the binary depending on the OS version. This technique doesn't work with 32/64-bit or PPC/x86 because you can't mix those within the same process, so you have to be running the right code from the start.

----

So simply, is there a way in Xcode 2.1 to compile a native Cocoa project so that a universal binary will be created that can be executed on PPC (10.2, 10.3 and 10.4) and i386 (10.4)?

*Yep, use lipo. See above.*

----

Ok, still a little confused. I see that you can use lipo to combine binaries, but does that mean that there is no way to compile 1 binary in Xcode that will run PPC 10.2-10.4 and x86 10.4? Do you basically have to use GCC 3.3 for 10.3 and below, and then GCC 4 for 10.4 and above?

----

Apple's posted a tutorial on how to compile a universal binary to run on pre-10.3.9 systems!

http://developer.apple.com/documentation/General/DeveloperTools/Conceptual/cross_development/General/UniversalBinaries/chapter_4_section_1.html#//apple_ref/doc/uid/TP40002983

You basically have to add these settings to your project (Target > Get Info > Build tab)

    
GCC_VERSION_i386 = 4.0
GCC_VERSION_ppc = 3.3
MACOSX_DEPLOYMENT_TARGET_i386 = 10.4
MACOSX_DEPLOYMENT_TARGET_ppc = 10.2
SDKROOT_i386 = /Developer/General/SDKs/MacOSX10.4u.sdk
SDKROOT_ppc = /Developer/General/SDKs/MacOSX10.2.8.sdk

