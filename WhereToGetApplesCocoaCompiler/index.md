---
layout: page
title: WhereToGetApplesCocoaCompiler
---



In order to program in OSX, you need a computer running OSX and a compiler (an application that converts source code to binary machine-readable code). For your development environment, why not use Apple's IDE, called General/XCode. It is free as part of your OSX system software and you can install it from your system installation disks (CD or DVD). The Developer Tools are installed  a folder on your boot drive called "Developer". The General/XCode IDE application will be in the /Developer/Applications folder. The Developer Tools also includes General/InterfaceBuilder, an application for prototyping your graphical user interface and more.

You can download the latest version of the Developer Tools from the General/AppleDeveloperConnection:

http://connect.apple.com

You must join General/AppleDeveloperConnection (but a basic membership is free). Once you are a member, login and go to "Download Software".  Download the most recent version of the Developer Tools  (the version current as of 12 Nov 2007 is Xcode 2.5).  The file is around a 902.9 MB download, so have something else to do while you download, unless you have a broadband internet connection. Earlier versions are made available on these servers.

Xcode uses the GNU Compiler Collection (GCC) compiler from the Free Software Foundation. Note that Apple does not, in fact, use the FSF version of GCC. Their version has several added features including Objective-C++ support and some different optimizer flags. According to posts on the GCC mailing list Apple would like to be able to use the FSF version, but several things need to be finished before this can happen.

----
**Actually, Apple provides Xcode with the Mac OS X Tiger and Leopard install discs. That's how I got my copy. All you need is a new iMac or some other Apple computer. Pop in Mac OS X Install Disc 1 and open up Xcode Tools folder on the CD contents in Finder. - Pietro Gagliardi http://web.mac.com/ **

----
Xcode can be used with OS X 10.3 (Panther) or later versions. If you are using OSX 10.2 or earlier, you can use Apple's old IDE, General/ProjectBuilder.  It is available at the above link.

General/ProjectBuilder uses the GNU Compiler Collection (GCC) v3.1 compiler from the Free Software Foundation, for C, C++, Objective-C, Ada, Fortran, and Java. And you can also build software using the command-line utility pbxbuild. You can also use GCC 3.3 with General/ProjectBuilder if you get the update from Apple.

----
Many developers don't even use General/ProjectBuilder, but they use other OSX text editors, or UNIX text editors.  

See General/PollEmacsProjectBuilderVI.

See also http://developer.apple.com/techpubs/macosx/General/DeveloperTools/gcc3/gcc/index.html
