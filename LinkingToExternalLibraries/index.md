---
layout: page
title: LinkingToExternalLibraries
---



I am writing a little GUI frontend to MySQL; I need to access libmysqlclient.a and libz.a. Initially I wrote a little command line test bed on Linux compiled with GCC; then did the same on OS X; then tried to do it in Project Builder.

Figuring out how to tell the linker where the lib files are was not possible. On the cocoa-dev list, the answer seemed to be "Add Files..." under the Project menu, which indeed works (and my testbed compiles and runs fine).

How do you set the library load path in ProjectBuilder? --SteveWainstead

----

Seems to me that you found the solution but another thing to consider is this: if you drag and drop the file (.a files in your case) into your Groups & Files list in your project you will be greeted with a sheet that allows you to choose the Reference Style (Default, Group Relative, Project Relative, Absolute Path, Build Product Relative, Search Path) among other options. Is this what you are looking for?

-- JoeZobkiw

----

It's funny how the solution is often to do some kind of graphical thing like that; after ten years of Linux as my primary OS I'm still accustomed to doing everything from the command line.

What would the equivalent be for doing this drag and drop via the menus, if any? -- SteveWainstead

----

How can I load Libraries at run-time ? I've tried it with the C-Header <dlfcn.h> but this header doesnt exist on MacOsX ...any ideas ?

Thanks
-- Lukas Westermann

----

man ld, man libtool, man dyld, man Mach-O for a start

/PerSquare

----

W.R.T. Drag & drop menu equivalent, try "Add Frameworks..."

W.R.T. linking at load time, look at /usr/include/mach-o/dyld.h -- I think NSAddLibrary may be helpful. See also OmniGroup and Darwin mailing lists, especially some older messages from Wilfredo Sanchez -- it's been covered in detail before...

-- MikeTrent 

----

I'm not much of a programmer, but I'm also comming from the unix side of things and would expect to be able to set a defualt path where ProjectBuilder will look for libraries.  Can this be done?
-todd

I think what you're looking for is the "Search Paths" page in the target settings; it's right below Linker Settings. -- Bo
----

I have to admit to a bit of confusion on a similar topic.  I included libsqlite.a in my current project thinking that I could just statically link it in and avoid all the foofara dealing with copy phases and framework paths.  However, when I ran it, I got a warning of the form "Prebinding disabled because required library **libsqlite.0.0.0.dylib** is not prebound". [emphasis mine].  That library does exist in my /usr/local/lib folder, but I had not added it to my project.  So, any ideas what's up with the switcheroo?  Why am I trying to include one and getting the other?  -- Bo

I had the same problem here, and I don't know why it happened either. However, if you chmod 0 libsqlite.0.0.0.dylib (or just delete it), XCode/ProjectBuilder goes right ahead and links in the static library. Totally weird. Anyone got a clue to what's going on? -- AlisdairMcDiarmid

I actually found out later what's going on: you see, when you add a library to your project, you may naively believe that you're actually telling XCode/PB to link against that library; this is not the case.  What you're really telling XCode/PB is to include **the directory** in which the library is found to its list of directories to search for libraries.  Now, if XCode/PB linker finds a *better* library in it's search path (and somewhere in that definition it's decided that dynamic libraries are *better* than static ones), then it'll choose that instead.  As for the solution, you can either 1) fiddle with the directory structure so that there isn't a dynamic library in the same directory as the static one, such as copying the static library to your project folder and including it from there or deleting the dynamic one (not recommended as this will break dynamically linked applications), or 2) go into the Search Paths and set the path to point directly to the static library (e.g. /usr/lib/libsqlite.a instead of /usr/lib/)  -- Bo

----

I'm trying to build a small Jabber client that uses the gloox-engine.
I've installed the gloox library [http://camaya.net/gloox] and now I want to use it (of course)

I've dragged the libgloox.a to my project and chosen absolute path.
Then I import the .h files I need (i.e., client.h)

Here's a codesnippet to show how I try to use it but it doesn't know what Client is...
It doesn't recognize it as a class...

*see note on entering the library and header paths in the build settings, just below - this sounds like the problem I had, also with a C++ library*

    
       Client *myClient = new Client( "user@jabber.org", "password" );
	myClient->registerConnectionListener( this );
	myClient->registerPresenceHandler( this );
       myClient->connect();


Does anyone know what I'm doing wrong here?
I use a .mm file so I should be able to use the C++ methods directly (I think...)

My import line is quite simple; just
    
#include "client.h"
#include "gloox.h"


The error I get is 
Unknown variable "Client" and that it expects a ";" after Client...
e.g., it doesn't understand that it is a class

----
Hmm.... Client is a pretty generic name... is it in a namespace by any chance? If so, you either need to     using namespace X; somewhere, or you need to prefix your use of     Client with that namespace.

*yes, there are all sorts of other things that could be going wrong, but try the hint below just to make sure*

----
If you are a beginner, you have chosen a project/operation that is probably going to take you out of your depth. You might want to check on the gloox site.
I have in the past tried (and failed) to use static libraries, but have found it easy to use frameworks. If I were working with gloox, I would download the sources, and create my own Gloox.framework.
There are no archives for the gloox mailing list, but if you ask for help there, you may be able to get advice from the Mac OS X maintainer for gloox, or may find yourself appointed to that role!

----

Just on the off chance that you haven't tried this, be sure that you have specified the paths to your static library and headers correctly in the Target build settings for the project. I have linked some simple (I repeat, simple) projects with static libraries (under Panther) and found that I had to straighten this out before the build would succeed. This advice comes because I added the static library to the project the same way that you have (and always got build errors whenever I moved the libraries around on the disk).

