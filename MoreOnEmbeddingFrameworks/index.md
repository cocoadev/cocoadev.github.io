---
layout: page
title: MoreOnEmbeddingFrameworks
---



Here's a quick HowTo on embedding frameworks into an application. This example will use MOKit framework project.


* Make a copy of the entire MOKit project (option + drag the MOKit project folder to create a copy of the project).
* Rename the new project folder MOK<nowiki/>it_embedded.
* Open MOK<nowiki/>it_embedded in ProjectBuilder
* Click on the "Targets" tab and select Settings/Simple View/General Settings
* Change the "Product Name" to MOK<nowiki/>it_embedded
* select Settings/Simple View/ Installation Settings
* Change the "Path:" to "@executable_path/../Frameworks"
* Build the framework


You now have a framework build that can be embedded into an application. The method to create your own custom embedded framework is the same as creating a normal (shared) framework, except you just change the install path to "@executable_path/../Frameworks" (see the MOKit example above if you missed how to change the install path).

I changed the name of the framework project to "MOKit_embedded" to avoid any confusion about which framework is being added to an application. This change has a downside though. You have to understand how frameworks are imported into a source file. 

    
#import <MOKit/MOKit.h> 


is the normal way to import a framework (notice that MOKit is repeated in the import line). What this line is telling the compiler is that the header MOKit.h is in the framework named MOKit. Since you changed the name of the framework project from MOKit to MOKit_embedded you have to change any source files that import MOKit to the following:

    
#import <MOKit_embedded/MOKit.h> 


You could avoid this by not changing the framework project name to MOKit_embedded, but I think that could cause more headaches if some of your apps use embedded frameworks and other apps use shared frameworks. 

The way you add an embedded framework to an application project is the same way you add a shared framework. The only difference in the case of adding the embedded framework "MOKit_embedded" is that you have to remember how to import the framework in your code (i.e. #import <MOKit_embedded/MOKit.h>). If you really want to stick to convention then you can change the name of MOKit's header from MOKit.h to MOKit_embedded.h, but that's up to you. If you decide to change the name of MOKit.h to MOKit_embedded.h you also have to change the way you import the framework in your code to:

    
#import <MOKit_embedded/MOKit_embedded.h> 


But remember that if you do this, any classes inside the framework that import "MOKit.h" will have to be modified.

--zootbobblu

----

It seems that you forgot to go into how one should add the Copy Files Phase to their application target to actually copy the framework into the app's bundle. Kinda important, IMO. ;-)

I am actually having a problem doing exactly this. There is a nice tutorial over at cocoadevcentral.com on doing this, and I followed it exactly to make both MOKit and EDCommon embeddable... but it won't work! If I ever move either of the frameworks out of my /Library/Frameworks folder, the application won't launch because dyld complains that it can't find the framework(s). Both frameworks have their install paths equal to @executable_path/../Frameworks, and I have both of the frameworks included in my app's target, and both of the frameworks are in my File Copy Phase (which is my first build phase). After my app is built, both frameworks reside nicely in my Application.bundle/Contents/Frameworks folder, but dyld still seems to be looking in /Library/Frameworks.

Did I miss something here?

TIA,
MarcWeil

----

It is amazing how many things can go wrong when doing this, it is a pain to get correctly. I am usually struggling with the OmniFrameworks, MOKit was not that much of a problem. Some hints 

* Delete your whole build folder when you go from development to release build
* I am not really sure about this, but i think PB might choose the wrong lib if you have one in your ~/Library/Framekworks/ folder 

I am actually using two folders for building right now, one is the development folder, it contains a copy of a debug build of the library (MOKit in this case), the library path is set to build folder relative. The release.build folder contains an optimized version of the library in a different folder. I do the release build via a script (man pbxbuild) a script calls pbxbuild with the release.build folder as its build folder, the correct (local) version of the library is taken. This has worked very well for me. Also MOKit suggests a different way of including frameworks, it uses a post processing step to change the install path (look at mokit.sourceforge.net it is in the readme).

Hope this helps HaRald

----

Right I forgot to talk about how to add the copy build phase. Here's a link that talks about it:

http://developer.apple.com/techpubs/macosx/DeveloperTools/ProjectBuilder/
ProjectBuilder.help/Contents/Resources/English.lproj/Frameworks/chapter_19_section_3.html
(wrapped url, which is broken, so this page isn't hugely wide)

I also realized that simply making a copy of the MOKit project folder and renaming the product MOKit_embedded is kind of a hack. This does serve the purpose of building a separate framework that can be embedded into an application, but this probably isn't the right way to do this. I tried duplicating the target and changing the product name, but that doesn't work. 

--zootbobbalu

Oh yeah, I got MOKit embedded into an application and everything works, so I'm not sure why runtime is looking in the wrong place in your case. The only thing I can think of is maybe when you added the framework to the project, the path you gave was for the non embedded build (if you changed the build location for the embedded version of the framework). I only say this because the default build setting for frameworks is to define the framework path as $(LOCAL_LIBRARY_DIR)/Frameworks (i.e. /Library/Frameworks). One way you could check to see if you actually have a framework built to embed into an app is to look at the raw data of the framework. There are parts of the Mach file you can read, and one of the parts in text form is the path of the framework. You should be able to vi the framework file and see @executable_path/../Frameworks/MOKit_embedded.framework/Versions/A/MOKit_embedded if everything was set up right. If you noticed I changed the name of MOKit to MOKit_embedded to avoid this type of confusion. 

----

You can check with otool to see if your application and embedded framework are linked correctly like this:

    
$ cd build
$ otool -L Appname.app/Contents/MacOS/Appname
Appname.app/Contents/MacOS/Appname
        /System/Library/Frameworks/Cocoa.framework/Versions/A/Cocoa (compatibility version 1.0.0, current version 8.0.0)
        @executable_path/../Frameworks/MOKit.framework/Versions/A/MOKit (compatibility version 1.0.0, current version 1.0.0)
        /usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 63.0.0)


Notice the line that begins with @executable_path. That means that MOKit.framework has been linked correctly, had it been an absolute path like the lines with Cocoa.framework and libSystem.B.dylib, users who installed your application would not be able to launch it unless they too had that framework installed in the correct location.

-- TheoHultberg/Iconara
----
Anybody got an updated URL to apple or somewhere on emebedding frameworks into an app? for having it copied into the app on build etc.  

tho currently when I do a build in deployment it fails to find it even tho for now it's in /Library/Frameworks/
-JeremyK

----
Introduction to Mac OS X Frameworks

http://developer.apple.com/documentation/MacOSX/Conceptual/BPFrameworks/index.html

PriceRingo

----

Tip: Make sure that the INSTALL_PATH setting that is changed (@executable_path/../Frameworks) is the target setting, not the project build style setting. Don't know why, but I changed the build style and it didn't take. Switched it to the target setting and all was well. (Xcode 1.5) � BrentGulanowski

Oh and there's this if you didn't find it on Google: http://rentzsch.com/cocoa/embeddedFrameworks

----

http://rentzsch.com/cocoa/embeddedFrameworks is a nice QuickTime howto. The only thing that wasn't clearly explained was how to drag one projects icon into another project. I wasn't aware of this feature so I jumped at the chance to make these interproject dependencies active in a couple of my applications. I got stuck trying to get the project icon to appear in the window's title bar (if you watch the movie you'll know what I'm talking about). Anyways, to get the project icon to appear in the project's main window title bar, just select the project item at the top of the groups and files pane, toggle from the embedded editor (command + shift + 'E') to the project's file info view (make sure none of the editor is visible) and then click on the view. You should see the title bar go from displaying the name of the current source file being edited to just showing an XCode icon with the name of the project. --zootbobbalu

----

This worked for me using Xcode 2.1:
(from my own notes)

1. Make sure the framework bundle ".framework" appears in "Groups &  
Files "Frameworks > Linked Frameworks"  group (if this is a third  
party framework you can drag it here).
2. Create a new copy files build phase after the Copy Bundle  
Resources build phase
3. Drag a copy of the framework to this new Build Phase
4. In the build phase inspector change the "Destination:"  to  
"Frameworks"
5. Remember to import the framework into your code with something like:
#import <ZNFramework/ZNFramework.h>

----

(Solution to another issue:) I got a build error "No such file or directory" until I noticed that a space in a folder name in the path to my project files caused the build system to split up the path like this (note extra -F):

-F/Users/paul/Projects/My -FProject/dev/Appname

To fix this, in my target's buld settings, I set Framework Search Paths = "$(SRCROOT)"  (including the quotes, that's what avoids the problem). This may not be a prefectly correct build setting, but it's working for me. -- PaulCollins

----

I've got a simple problem when I create a project name that has a space in the name. Xcode builds the project just fine and has no problems until I include an addtional 3rd party framework, then the build fails when trying to include the framework in the finished app. For a test I created a project called "The Tester" on my desktop and built it; then I tried to include the RBSplitView framework in the project (something I've done successfully before in test projects - none of which had spaces in the project name). When I build the project I get these errors/warnings

    
Tool:0: Command /usr/bin/gcc-4.0 failed with exit code 1
Tool:0: can't locate framework for: -framework RBSplitView
Tool:0: warning -F: directory name (Tester/Source) does not exist
Tool:0: warning -F: directory name (/Users/colin/Desktop/The) does not exist
Tool:0: warning -L: directory name (Tester/Source) does not exist
Tool:0: warning -L: directory name (/Users/colin/Desktop/The) does not exist


I tried changing the addressing on the framework to absolute (full path) and relative to build directory, etc and nothing works. I used the same procedures I do for every framework (1) Add framework to project (2) Move frameworks to under Frameworks -> Linked Frameworks (3) Add copy file phase to target, select Frameworks in Pop up button and then close window. In projects with no spaces in the title the project builds fine with any framework I test, in any project with a space in the title it generates the above errors trying to include any framework in the project.

Is there a workaround or anything I can do to fix this short of creating a new project that has no spaces in the title?

----

See remarks just above by PaulCollins; the fact that your project fails only after you add the framework led me to move your question onto this page. *Plus �a change, plus c�est la m�me chose*. Hope this helps.

---- 

The project builds fine now, however it still generated a couple warnings

/usr/bin/ld: warning -L: directory name (/Users/colin/Desktop/The) does not exist
/usr/bin/ld: warning -L: directory name (Tester/Source) does not exist

so I also changed the library search path from

$(LIBRARY_SEARCH_PATHS) $(SRCROOT)/Source

to

$(LIBRARY_SEARCH_PATHS) "$(SRCROOT)/Source"

And now the project builds fine with no errors and warnings. Thanks for all the help!

----

I thought i'd do a final followup, idk why but it appears Xcode is randomly deleting the quotes I mentioned above. Since I only have 1 space in my projects title i figure i'll create a project name with no spaces and change the product name in the build settings so the final binary has the desired name I want.

----

----

If you're getting this problem in 2007, and the symptoms are the same as this person: http://www.cocoabuilder.com/archive/message/xcode/2006/2/23/4298 then try upgrading your Xcode to the latest (currently 2.4.1), linking with the OSX 10.4u SDK, and if you're on a PPC, try turning off intel builds.  This worked for me when I had the same problem.

