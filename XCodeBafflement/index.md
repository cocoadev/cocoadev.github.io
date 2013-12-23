---
layout: page
title: XCodeBafflement
---

Xcode baffles me, scares me, and leaves me feeling like a babe in the woods. It took me the better part of an afternoon just to figure out stuff I used to take for granted, like how to make headers in a framework target be public or how to make my target debuggable.

Xcode seems to still have everything PB had, but a lot has been 'relocaed'. That's OK, I'm adaptable -- hell, I've developed for embedded motorola chips, win32, General/BeOS, and so on. I pride myself on my ability to learn a new environment.

What bothers me is that Apple just assumes we'll step right in an grok it. The documentation doesn't seem to have ( at least, I haven't found it) anything that canhelp me transition from PB to Xcode. Sort of a "What's new, what's different" thing. It seems the docs all center around how to do "Hello World" apps and gloss over the hard stuff -- which is what I need.

Well, I'm asking the good people of General/CocoaDev -- if anybody's been lucky enough to beta test panther/xcode, and if you feel like you understand it and work well with it, can you write up a document for people like me to help us learn the transition? And I'm not talking how-to-write-a-hello-world, the first thing I did with Xcode and the new IB was to write a hello world. What I need is:

-how to make and manage multiple targets, with complex interdependencies

-how to use the new groups-pane

--General/ShamylZakariya

----

Welcome to the club! :-)

It took me about an hour to create a project that builds a trivial framework and a trivial tool that links against the framework. Editing target information is more hidden than it used to be, or so it seems. I finally found UI that let me set a target dependency (i.e., the tool depends on the framework) but it had no effect on my project build. I eventually got it to work by manually configuring all the dependent file information, but I don't remember what I did at all. So I'd say Xcode isn't currently up to the task of "managing multiple targets, wtih complex interdependencies", IMO.

Still, it seems intriguing for simple apps. I'll second the call for someone to straighten all this out.

-- General/MikeTrent

----

Whoa, MT baffled. Scary stuff. 

The interface is a serious change...I'm still lost half the time (wish they would put the project build settings back in the project menu where I can easily change it for any target I may be build). The build window is also annoying... I want the sub window for code to be added back so I can fix those annoying syntax bugs without opening a new window.

Fix and Continue took a while to work out, but its happy now. Distributed builds is not working on my system (even with gigabit ethernet or IP over Firewire). Quite annoyed there. 

General/XCode is not crashing anymore fortunately (or at least I have learnt the ways around its crashes). 

----

Has anyone else had trouble with General/XCode suddenly becoming confused as to the whereabouts of project files? I haven't done a lot with it, but I already had a problem when it said it couldn't find a .nib file that I hadn't moved or anything, and the nib file still appeared in the project window and could be edited, saved, etc. The program even ran, but if you put it on another computer and ran it, it immediately closed with no error message. I finally just made a new project and had some fun copying and pasting.

*...it immediately closed with no error message* -- sounds like a Development-Deployment issue ( see General/ZeroLink)

Now, today, it's telling me it can't find the whole project folder even as I sit there and edit it. Again, I haven't touched any folders.

It also always flashes the aforementioned error messages really fast, as I quit. I have to take quick screenshots so I can even read them. Should I just re-install this sucker?

%%LINE
You need to go into your utilities folder and open the console log.  It will tell you what error message you are getting.  If you see the words General/ZeroLink anywhere in the error message (which is what it sounds like to me too), you need to compile the complete project as "Deployment" instead of Development.  You get there in Xcode by clicking on your project icon (in the tree control), and choosing "Get Info" from the project menu.  Click the "Styles" tab, and change the drop-down to "Deployment".  I'd clean your target, recompile, and then try it on the other machine again.

I had a similar problem with my project and this fixed it.
----

Xcode's driving me crazy with this thing it does...flashing error messages as I quit. They usually say something like 'Can't find General/MainMenu.nib' when General/MainMenu.nib is, in fact, listed in the project's Xcode window and the application will run fine.

I've tried cleaning the targets. This problem has happened to me with every Xcode project I've worked on over a period of more than a few days, and the only sure-fire way I've found to solve it is make a new project and then bring everything from the old one into it. That works OK for something small, but what I'm working on now is just too big to keep doing that. I'm running Xcode 1.2 on 10.3.4...this happened under Xcode 1.1 too though.

----

Sometimes rebuilding the project's indexes has solved this problem for me, but other times even this step fails.

----

I am suspicious that this bug comes up when the nib file has been opened from within the Xcode project. Then (not sure when) something goes wrong when the project window (or maybe it's some other window) is closed, under some circumstances. Or maybe it's something else. You get the idea.

I have been trying to open my nib files only from the Finder when working on projects. So far, I haven't had this error again.

*seems to be fixed as of Xcode 1.5 and later*

----

I keep getting an error when trying to compile any of my projects, and even new projects. It always gives me the error, "can't locate file for: -lcrt.o". Nothing I can do will fix it - I've reinstalled Xcode 2 times, archive and installed Panther, then reinstalled Xcode, and still get the error. I've done the usual bag of tricks - checking permissions, etc., still no luck.

----

Apple had a bit of schizophrenic behavior which could cause this error. These files are essential to building any application, but Apple couldn't decide whether they should be in the OS installer or the Xcode installer. So they moved them back and forth a couple of times. The end result is that if you do the wrong sequence of OS installs and Xcode installs, you can end up without them.

The best solution is to download Xcode 1.5 from Apple, which should have everything you need. You probably have 1.1, and 1.5 is way better, so this is a good idea anyway.

If you really want to fix your current installation rather than download 1.5 from Apple, or if you've already tried that and it doesn't work anyway, e-mail me (mike@mikeash.com) and I'll hook you up with the necessary files.

----

I've heard quite a bit about how awful General/XCode is.  However, at our company, everyone has updated to General/XCode and Panther, and everything is going fine.  We've reincorporated our version control system, almost all of our past projects have been imported without a hitch, save the minor glitch that forced us to update a little which is actually a good thing.  Further, General/XCode is extremely fast and stable.  No one in the dev department has had any crashes yet and we've been pounding on the thing today officially all day, and unofficially since it was in beta.  We write software that is specifically tuned to Mac OS X and Unix systems (read separate platforms) and everything is running well.  Also, we love the new interface, everything is streamlined, and fits in with our Extreme Programming perspective much more nicely than Project Builder.  All-in-all, an excellent product.  While I'm advocating General/XCode, perhaps I should give a little background on what we did.  Complete reformat of every developer machine (remembering to backup everything to General/XRaids of course), clean install of Panther on every machine (please note: just as a test we decided not to use Server to do this; we wanted to catch variations in hardware if there were any the old fashioned way).  Once Panther was installed everything was returned to the machines (read project files and other data files; nothing that was a part of any older system was used).  Everything has been working fine.  There was a minor glitch at some point when the repository became unstable, but it turned out to be a human error, and we were back on track within a span of 30 minutes (after we figured out it was human error).  I don't know if every instance of instability is a specific case or linked in some form to something else, but many of the developers I've spoken to today, within and without the company, have voiced their delight with the results of General/XCode.

----

In case anybody's wondering, I learned this tonight: here's how you get General/XCode to link a program against a static lib that is a target in the same project. In case you're wondering why -- I have a number of projects that build embedded frameworks for another app (in a separate project). Since I'm a believer in testing everything, thoroughly, I compile also a static library and a number of executable target test-cases which evaluate an aspect of the framework. Since the framework's built to be embedded, I have to link against a static version ( e.g. libname.a ). So, in PB, I'd just make the static target, and while my current target is the test-case, I'd just click on the little bullet in the files pane that equated to "include this in the build". 

In General/XCode, however, you have to make a new "Frameworks and Libraries" build phase, and then drag the library into it. I figured it out by *guessing*. From the Info panel, make the library a dependancy of the test-case, so compilation is done in order. And whammo, it works. Admittedly, it actually makes sense... but I wish I had actually found documentation to clarify this difference in project management. Too bad.

--General/ShamylZakariya

RE: Static Libs in General/XCode

Nice one Shamyl - this has been bugging me for days. I had the same "idea" to add a new Build Phase - but the documentation doesn't mention it anywhere. This only problem is - my project doesn't let me add a new "Frameworks & Libraries" phase. In fact the only phases I can add are "Applescript", "Java Archive" and "General/ResourceManager Resources".
-- General/JustinSaunders

----

I did target dependencies first thing when I started using Xcode... I just selected Target A which was supposed to depend on Target B, and dragged Target B into the list-pane that appears in the project window next to the groups pane. No problem...

The groups pane is just a system of queries. You've got all your files in one mass lump, whether they be source or resources or whatever, and then stuff like the smart groups lets you show e.g. all files whose names match such and such a regex. (I wish there were more options for this, like "file is used by selected target" or "file does not compile" or whatever, but those can be for the future, I guess.) So the first one is a group that expands to show all the "dumb" groups used by your project... the stuff that looks like a file system. Project/Classes/General/MyFunkyClass.h, or whatever.

And if there's no files returned by the query, it's greyed-- like the warnings and errors group.

And the project symbols one is awesome. I was using it just yesterday to show all my dealloc implementations. Very nice.

As for the other stuff, ask and ye shall receive (an answer or a stupefied "what?")

-- General/RobRix

*Project Symbols, how is this used? for me it is just an entry (not a group) which ignores a double click and shows no info in the inspector.*

For me at least, the Project Symbols shows me all the methods and declarations in my code. I've only tested it against Objective-C... since 90% of my code is c++ I don't know if it will actually be useful to me or not... nonetheless, it's very nice. For what it's worth, I'm beginning to grok the new groups pane -- it not only makes sense but it's also quite useful. Nice that it's keyboard navigable as well ( IMHO, the best thing panther brings is reasonably consistent keyboard navigation of tree views ).

I think the main concern I've had is the use of the new Info Panel to set up build settings, dependancies, etc etc. I'm accustomed to the old PB way of doing it; it's just a matter of time. In principle it seems well thought out, but frankly I liked the old way, simply because it was a very transparent model of the internal build system. I'm sure with use, the new way will become similarly clear.

Aside from the target management, I'm converted ;) It only took a few days... which I suppose speaks well of General/XCode.
--General/ShamylZakariya

----

Xcode is probably OK. But it's not stable. It crashes for me all the time. PB wasn't that stable either, but Xcode is really bad. Sometimes it just dies when in the background, without explanation. Buggy buggy buggy. --General/TheoHultberg/Iconara

----

When I installed Panther, I chose to Upgrade and not a Archive/Restore or a Clean Install. When I installed the new Xcode, I did not remove my original developer tools which were Project Builder plus the Xcode preview. There is obviously a conflict when you try this.
----
LOL, To further follow up on this, All I have to say is; I wish this tech note came a couple of days earlier...
http://developer.apple.com/qa/qa2001/qa1321.html
----

My project doesn't compile with Panther. I think that the zlib is causing the problem, but I have no idea how to fix it.
I have "-lz" in "Other linker flags" and import it with "#import <zlib.h>". I get the following error. Any Ideas? :( -- JP

    
Undefined symbols:

 ___setonlyClocaleconv referenced from General/CoreFoundation expected to be defined in libSystem

----
I'll try to re-install developer tools -- JP
----
Re-install didn't help too, but I "fixed" the problem. Good thing that I had made a backup from my project before opening it in General/XCode. Backup version compiles just fine, but both projects are identical!!! -- JP


----
I want to link the libSaturn.dylib to my project ( see saturn Tool ) and of course the �finstrument-functions settings ( see General/SaturnUserGuide.pdf ).
Where do I put my �finstrument-functions parameters. 
thanks

Ren�

Ok, I think I got this right, but I get a error in a text file back with ' SATURN_BE: endThreadCalltreeAndIcicles: value is NULL.".
I realy don't know what this means.

Ren�

----

The links on the General/XCode page ought to help.

----

Strangeness this evening. In development, I declared a new integer and it had an initial value of 31. In deployment, its initial value returned to normal, or 0. Is this expected behaviour?

----

If that was an instance variable, then that is strange. If that was declared within the scope of a function, than the integer can be anything because it is up to you to initialize the value.
----
This is not really an Xcode issue. It sounds more like a gcc issue, if there is an issue at all. An instance variable that you declare in an Objective-C class and do not initialize in an     - init method should have a default value of Zero (0). A local variable that is not initialized can have any value and should not be relied upon (as mentioned) as per the C and C++ standards. An instance variable of a C++ class, that you did not explicitly initialize in the class constructor, will also have an indeterminate value.

----

If you get the following error:

    
Couldn't open /Users/username/Desktop/General/ChemBuddy-1.1a3 Source/General/ChemBuddy.pbproj.
Reason: didn't find classname for 'isa' key.


It is because the project was created with General/XCode, and General/ProjectBuilder will not open General/XCode projects. If the prefix file has the .pch extension the project was created with General/XCode.

----

I'm trying to follow the book Building Cocoa Applications : A Step by Step Guide, however all the instructions in the book are for Project Builder and I can't figure out how to do them in X Code. Is there any kind of tutorial or page which can explain the differences between X Code and Project Builder? Also what is the best way to learn Cocoa? I have quite a few books on it but I need to sit down and read them sometime.

----

Surely not ALL the instructions are incomprehensible...

Many substantial differences from PB to Xcode seen by beginners have to do with the different ways the projects maintin the Info.plist for the project.

If there is a specific sticking point, I am sure there are many here who are more-than-willing to step up and help.
I, for example, also own a copy of this book, and if you cite a page number with material that is confusing you, I can draw you a figurative map,
even if I cannot actually dig up a Rosetta stone for you.  ;)

----

I'm new to Cocoa, too.  I bought 'cocoa programming for Mac OS X' (Hillegass).  It's a great book for learning and takes you step-by-step.  In most cases, when I wanted to 'do' something, I found a chapter on how to get it done.  

----

In response to the OP: maybe you can get Project builder from an old installer or somebody and use it while reading the book. Once you better understand the whole thing, you can start using Xcode and figure out the differences

----

Does anyone know of anyway to convert General/XCode projects to Project Builder, other that creating a new project, and copying the files? 

*Yup. But that's how you do it.*

----

I second the call for a book on Xcode---what do all of its gazillion parameters do? How does the new compiler differ from the old IN DETAIL? How does the debugger differ from the one in Project Builder? etc. etc. etc.

I am especially interested in cross-development options and why enabling cross-development seemed to be required for my old Project Builder project. I have an application which built successfully under Project Builder and runs successfully on all OS X versions to date. I opened it with Xcode and tried to build it. I used all default settings in Xcode and didn't change a single line of code. ** I got about 5000 compile errors!**. To get the project to build successfully I had to do two things.
  *I changed a setting to make the compiler compile everything as Objective-C regardless of the filename extension (my project is entirely Objecctive-C except for one plain C file). * I had to enable cross-development and set the target system to OS 10.1 

The first fix got rid of all but about 500 errors. After the second fix the project built successfully. I am getting one warning at link time, but the application seems to run fine on 10.1 and 10.3. I don't understand why either of those two corrections were necessary.

When I last used the project in Project Builder I had several break points set. In Xcode I added a couple more, but in debug mode the breakpoints were not honored. And no, they had not been disabled! I had to delete all breakpoints. After that, new breakpoints worked. Unfortunately, I haven't had a chance to go back and set breakpoints exactly where the ones which didn't work had been. For all I know they still wouldn't work.

I have a text file used as a resource. After the first successful Xcode build my application could successfully read that file at run time. I then edited the text in Xcode. After that my application could NOT read the file at run time. I fixed this problem by deleting the file and adding it manually from a backup copy on disk.

My development and deployment builds are both approximately the same size---a little under 1 MB. But the development build is actually a little smaller than the deployment build!

The debugger has other problems besides not honoring breakpoints. Sometimes the gdb command window is available and I can type commands when stopped at a breakpoint. Other times it is visible but I cannot enter a command into it. I have not figured ouit any pattern to this behavior.

This last item may be misplaced here, but I don't know where else to ask. At the present time my application has localizations for English and Dutch. The lproj files are named English.lproj and Dutch.lproj. I read somewhere that these names are being deprecated and are not guaranteed to work in future versions of the OS. We are supposed to use "en.lproj" for English and either "nd.lproj" or "nl.lproj" (I forget which) for Dutch. When I changed the names of these folders in the Finder the application would not run. I tried changing the names from within Xcode but couldn't figure out how to do it. I notice that when you make a pre-existing file localizeable in Xcode, it automatically makes the old file "English", not "en".

** We need "Xcode---The Missing Manual"!**

----

It seems somebody listened: General/BookTheMacXcodeTwoBook

*can 'Xcode for Dummies' be far behind?*   **yes, far, far behind**

----

If you'd like to know more about Cohen and Cohen's "The Mac Xcode 2 Book" you can find the authors' site here:

http://www.digitalmedievalist.com/xcode/

The site's not "officially" public yet, but what the heck <g>

----
