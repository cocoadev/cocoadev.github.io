---
layout: page
title: IncludeLibExifInApp
---

Describe IncludeLibExifInApp here.


Hi all,
I would like to use this library in an Cocoa Application.
How can I proceed to include it in my Xcode project and use it (Xcode wait for Cocoa Framework, libexif is written in C).
And how can I compile this library as double binary (PPC and Intel binary), the objective of this, is to include this library in my application to avoid the installation of this library on the client work stations.
I always encounter this issue, how to compile a C library (from open source world) as double binary, and use it in Cocoa Application (I don't speak about the code, I know how to wrap the C in a Cocoa class).

thank you ;).


I answer to the first question, how to compile a C library as universal binary (http://developer.apple.com/technotes/tn2005/tn2137.html)
./configure --disable-dependency-tracking 
and just a 
make CFLAGS="-arch ppc -arch i386" 
Under Macos 10.5.2 with GCC 4.0.1

