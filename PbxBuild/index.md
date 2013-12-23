---
layout: page
title: PbxBuild
---




Command line tool to build ProjectBuilder projects. pbxbuild, pbxbuild clean, pbxbuild install, etc. Currently there is no way to do a "pbxbuild install" operation from Project Builder.app.

This is renamed to xcodebuild in Mac OS X 10.3 (Panther).

----

This is incorrect! You CAN install projects automatically using Project Builder OR Xcode. All you need to do is add two build settings:

    
SYMROOT = ${INSTALL_PATH}
TEMP_DIR = ./build/${PRODUCT_NAME}.build/${TARGET_NAME}.build


Make sure you don't have a DSTROOT build setting, as this will break what I have said. This feature is not only undocumented by Apple, but is MISDOCUMENTED! This will work, and it is the only way to do it from the IDE. --EliotSimcoe

PS. I'm working hard to make this known, but this is probably the most misunderstood feature of PB/Xcode.

----

You can also set DSTROOT to /
This is what we used in our build system. But the above way is better.

----

Does there exist a definitive, canonical listing of all the PB build settings.  It always seems like there's a way to get PB to dosomething, but you have to know the intereactions between a handful of build settings.

----

No, there doesn't seem to be. I think most of them are undocumented. This is improving in Xcode (where they give you concise listings of many of the build settings), but not all of them are present (like the SYMROOT and TEMP_DIR). I only discovered the method described above after half a day of messing around with xCode. --EliotSimcoe

----
In the *December 2002 Developer Tools Release Notes* there is a section called *Project Builder Build Settings* that outline them, including the ones you cite.
----
The complete list of build settings for Xcode can now be found in file:///Developer/Documentation/ReleaseNotes/Xcode/XcodeBuildSettings.html

or on the Apple Developer web site at http://developer.apple.com/releasenotes/DeveloperTools/Xcode/XcodeBuildSettings.html

