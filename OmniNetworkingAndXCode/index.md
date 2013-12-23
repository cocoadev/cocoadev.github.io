---
layout: page
title: OmniNetworkingAndXCode
---

Anyone else having problems compiling the OmniBase, OmniFoundation, etc. with XCode.  I generally get a bunch of warnings/errors, then it doesn't build.  I did OmniBase first, put its foundation into /System/Library/Frameworks
Then I try OmniFoundation, and I get all these warnings/errors.  XCode then, apparently, doesn't like this and craps out on me.  Using 10.2's project builder, it would wade through those, and compile ok.  Suggestions?

NOTE: If you are having trouble compiling your application with the Omni Frameworks, you may need to update your copy of Xcode to the latest version. (2007/09/29)

2006/07/14 ------ Tobias Haeberle - Added --------

The information presented on this page is out-of-date. Although I am a fresh Cocoa newbie, I might be able give a final solution to this XCode problem.
In order to easily use the OmniNetworking Framwork, follow these steps:

1. Download the DMG.


2. Copy the folder "OmniGroup" to whereever you want it to be

3. Change the file "projectList" inside the "Scripts" folder to look like this:

    
cat << .
OmniGroup/Frameworks/OmniBase 
OmniGroup/Frameworks/OmniFoundation 
OmniGroup/Frameworks/OmniNetworking 
.


4. Using the Terminal change directory to "<code>/path/to/OmniGroup/</code>"

5. In Terminal run the follwing command (see Scripts/README.txt for details):

<code> ./Scripts/Build Frameworks install </code>

6. The Framworks are being compiled and installed to <code> /Users/shared/YOUR_USER_NAME/Installed Products/ </code>

7. Copy these ".framework" files to <code> ~/Library/Frameworks </code>

8. Add the OmniNetworking and OmniBase Framework to your project.

Your are ready to use the OmniNetworking Framework!

If you want to deploy your software and be sure that it runs, even if the user does not have the OmniNetworking Frameworks installed, continue with step 9)

9. Add a new Build Phase: Menu -> Project -> New Build Phase -> New Copy Files Build Phase

10. From the PopUp Menu choose "Frameworks" and close the window

11. Check your "Groups & Files"-View on the left side. Open targets and click on the triangle next to your target.

12. You see a new build phase named "Copy Files". Now drag the OmniBase and OmniNetworking framework on this icon.

The Omni frameworks are now wrapped into your application's boundle!

2004/04/09 - Scott Corscadden - Added -------------

Yeah, I noticed this too - open the OmniBase.xcode file, not the .pbproj one. Then, the first thing is to select the top node in XCode (the blue icon for "OmniBase"), then "Get Info". I noticed that the "Cross Develop Using Target" setting is selected to MacOSX10.2.7.sdk, or some such. I didn't have the room for cross-dev, and didn't install them. I changed the pulldown from "Other" to "Current Mac OS", and it then seemed to compile fine.

------------------------------------------------

I'm also having issues... even with the new XCode projects they provide... I got OmniBase to compile, but OmniFoundation says something is linked to a dynamicly linked lib... 
OmniFoundation (Framework).build/Objects-normal/ppc/NSData-OFExtensions.o illegal reference to symbol: _crc32 defined in indirectly referenced dynamic library /usr/lib/libz.1.dylib
Not sure what to do...

2005/02/11 - hitoro ------------------------------

Edit the Build Settings for the framework (Inspector on target "OmniFoundation (Framework)" > Build > Linking > Other Linker Flags) and add "-lz" (without quotes) to the setting.

2004/10/14 - Eberhard Rensch ---------------------

Thanks for the tip, Scott! But now I've the next problem. I'm working on Mac OS X 10.3.5 with XCode 1.5. The Omni frameworks are downloaded as OmniXXX-2004-07-16.dmg files.

The linking fails with the following error message:

**Can't open: /Users/zaggo/develop/OmniBase/../../Scripts/Seg Addr Table.txt for -seg_addr_table /Users/zaggo/develop/OmniBase/../../Scripts/Seg Addr Table.txt (No such file or directory, errno = 2)**

So I realized, that there seems to exist a file **Seg Addr Table.txt** on an usual OmniGroup computer. 
Because I didn't found any Seg Addr Table.txt on the downloaded DMG-Drives nor on the Omni-Website, I tried to write my own.

This is my Seg Addr Table.txt (regular plain text file):

    
0x10000000	@executable_path/../Frameworks/OmniBase.framework/Versions/2001A/OmniBase
0x20000000	@executable_path/../Frameworks/OmniNetworking.framework/Versions/2003A/OmniNetworking

 
With this file on my disk and re-adjusting the linker flags (select target, e.g. 'OmniBase (Framework)', open the inspector window, search for 'other linker flags' and change the given path to the Seg Addr Table.txt file) the OmniBase and OmniNetworking frameworks can be built and linked without errors.
(for OmniNetworking you have to first build the OmniBase.framework and copy this into the build directory of the OmniNetworking project).

I didn't tried, but I think, it works also for the other Omni frameworks this way: Change the 'Cross Develop Using Target', add a line to your own Seg Addr Table.txt file for the framework and re-adjust the path in the linker flags preferences.

Finally I managed to build an application with the OmniBase.framework and OmniNetworking.framework copied in its bundle (Contents/Frameworks).
In my application project I added the two frameworks (Project>Add to project...) OmniBase.framework & OmniNetworking.framework I'd just built above. Then I've added a new build phase (Project > New Build Phase > New Copy Build Phase).  In the inspector window of this build phase I've selected 'Frameworks' as destination (Subpath was left empty, 'Copy only when installing' unchecked). Then I've drag&dropped the two frameworks from the 'Groups & Files' view to the 'Copy Files' section in the target section. That's it. 
I hope this will help some people!

2004/10/14 - Dirk Dittert ---------------------

I was also experiencing problems when I tried to compile the OmniNetworking Framework. The were mostly gone once I opened the Project Builder project instead of the provided XCode project. Additionally, I manually readded the frameworks from the correct location on my hard drive. After that, I was at leat able to compile those frameworks.

