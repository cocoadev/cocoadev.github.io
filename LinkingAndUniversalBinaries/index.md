---
layout: page
title: LinkingAndUniversalBinaries
---




I was trying to link a couple of static libraries in my app.
When using zerolink the build worked as expected but I just could not build the release version...

I could compile the app but when it started to link I got lots of "Unknown symbols:" errors

The project used the following static libraries from which the unresolved symbols came: libcrypto.a, libexpat.a, libssl.a and libz.a

The problem turned out to be that the libs had been built for PowerPC processors but my project was built as a Universal Binary.
So I just removed i386 from the Architecture setting in my Target - Release settings

further discussion of where to go from here is in HowToBuildUniversalLibraries

