---
layout: page
title: UniversalBinaryLinkingProblem
---



Hi, I have a PPC General/XCode project that I've worked on for a month or so on my PPC (G4) Mac, and now am trying to get things up and running as a Universal Binary on a new Macbook Pro.

I have read the Apple docs on converting to a universal binary, and followed them:

- The MacOSX10.4u.sdk is my SDK path for both the Project info, and the Target info
- Have added PPC to the architecture for the release build under both the Project info, and the Target info
- Have turned off Zerolinking for the release build
- Also added the application directory to "Library Search Paths" (have General/RBSplitView library in there)

The Debug settings build properly, the app works as under PPC. However, under the Release settings I get the following error at compile time (this output produced by a copy and paste fro the Build results window, this seems to give more info than is actually printed to screen):

    
    cd /Users/username/Documents/Development/Cocoa/Steel/Steel
    /usr/bin/gcc-4.0 -o /Users/username/Documents/Development/Cocoa/Steel/Steel/build/Steel.build/Release/Steel.build/Objects-normal/i386/Steel -L/Users/username/Documents/Development/Cocoa/Steel/Steel/build/Release -L/Users/username/Documents/Development/Cocoa/Steel/Steel -L/Users/username/Documents/Development/Cocoa/Steel/Steel -F/Users/username/Documents/Development/Cocoa/Steel/Steel/build/Release -filelist /Users/username/Documents/Development/Cocoa/Steel/Steel/build/Steel.build/Release/Steel.build/Objects-normal/i386/Steel.General/LinkFileList -framework Cocoa -framework Quartz -framework General/QTKit -lRBSplitView -arch i386 -Wl,-Y,1455 -mmacosx-version-min=10.4 -isysroot /Developer/General/SDKs/MacOSX10.4u.sdk
/usr/bin/ld: Undefined symbols:
_QTGetTrackProperty
_QTGetTrackPropertyInfo
/Users/username/Documents/Development/Cocoa/Steel/Steel/build/Steel.build/Release/Steel.build/Objects-normal/i386/General/LibraryController.o reference to undefined _QTGetTrackProperty
/Users/username/Documents/Development/Cocoa/Steel/Steel/build/Steel.build/Release/Steel.build/Objects-normal/i386/General/LibraryController.o reference to undefined _QTGetTrackPropertyInfo
collect2: ld returned 1 exit status
	/usr/bin/ld: Undefined symbols:
	_QTGetTrackProperty
	_QTGetTrackPropertyInfo
	/Users/username/Documents/Development/Cocoa/Steel/Steel/build/Steel.build/Release/Steel.build/Objects-normal/i386/General/LibraryController.o reference to undefined _QTGetTrackProperty
	/Users/username/Documents/Development/Cocoa/Steel/Steel/build/Steel.build/Release/Steel.build/Objects-normal/i386/General/LibraryController.o reference to undefined _QTGetTrackPropertyInfo
	collect2: ld returned 1 exit status


It seems to be having trouble linking _QTGetTrackProperty and _QTGetTrackPropertyInfo from the General/QTKit framework, to be sure that something hadn't moved as I brought the project across from my other machine I removed and re-added the General/QTKit framework from the project. No change.

Interestingly, I get the same results if I remove PPC from the Architectures entry in both Project Info and from the Target Info for the Release build. Still, Debug compiles and runs, Release complains as above.

I've Googled, but not found much that is relevant, I've also been through the differences between Debug and Release settings (in both Project Info and Target Info) to spot a key difference, but apart from little optimisation things and zero-linking, I can't find any glaring differences that would cause one to compile and the other to fail.

If anyone can offer a word of advice, it'd be very gratefully received!

(Also, can anyone shed any light on the relationship between the settings in the Project Info window, and those in the Target's info window. Does one override the other?)

Thanks!

----
The symbols you refer to are part of General/QuickTime, not General/QTKit. Link against General/QuickTime and your problem shold be solved.

General/ZeroLink is *the* important difference here. It basically ignores what you link against (there are exceptions to this) and looks up everything for itself. So treating General/ZeroLink as if it weren't important in this situation is completely wrong. If you turn it off for Debug (which I *highly* recommend, covering up problems of this nature is not good) then you will get the same problem in Debug.

----

Thanks for your help, of course that works perfectly. Thanks also for the advice about zero-link, I will take it :)
