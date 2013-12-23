---
layout: page
title: LinkingWorkerTaskToEmbeddedFramework
---



Does anyone know how to link a Cocoa Shell Tool to an embedded framework? I have an application with a worker task that I would like to link against an embedded framework. The problem I'm having is getting Xcode to compile the tool against the embedded framework. I get "ld" errors even though I have placed the framework in a directory relative to the tool's path (@executable_path/../Frameworks). 

My guess is that I have to modify a target build setting somewhere. Any help would be greatly appreciated.

--zootbobbalu 

----

If your shell tool is compiled as Intel it won't work. You have to compile against other Intel frameworks.

----

I thought any MachO file can be a UniversalBinary. Please note, I'm talking about a "Cocoa" shell tool. --zootbobbalu

----

PostYourCode. Or in this case, post your errors.

----

There really isn't anything to this. 


*Create an embedded Cocoa framework project (ServerClientSupport).
*Create a Cocoa application that links against the embedded framework (ServerClientSupport).
*Create a Cocoa shell tool that links against the embedded framework that is a resource of the Cocoa application.


The error I'm getting is:

    

/usr/bin/ld: warning can't open dynamic library: @executable_path/../Frameworks/ServerClientSupport.framework/Versions/A/ServerClientSupport (checking for undefined symbols may be affected) (No such file or directory, errno = 2)



The thing I don't understand is where @executable_path is pointing to when "ld" tries to open this framework. --zootbobbalu

----

Dumb question, but... is ServerClientSupport.framework actually in your tool's *Link Binary With Libraries* build phase?

----

Yep --zootbobbalu

----
AFAIK, I don't think you can link tools to embedded frameworks. A normal Cocoa application has a bundle structure like this:
    
Contents/MacOS/*myApp*   <-- the executable
        /Frameworks/*Embedded.framework*

    @executable_path is then set to the path of *myApp* when you run the program, which makes the embedded framework relatively located in the Frameworks folder of the parent folder (    ../Frameworks). Since a shell tool is **not** a bundle, there's no Frameworks folder, and you can't do this. However, you can make a faceless Cocoa application (see LSBackgroundOnly), ask the producer of the framework for a library version, or just require that users install the required framework. Hope this has been cleared up a bit. --JediKnil

----
This is true for a standalone tool. However, he's bundling both the tool and the framework in the application, in which case there's no reason this can't work. What's more, this error is happening at link time, not at runtime, where these issues do not yet come into play. -- PrimeOperator

----

*This is true for a standalone tool. However, he's bundling both the tool and the framework in the application, in which case there's no reason this can't work. What's more, this error is happening at link time, not at runtime, where these issues do not yet come into play. -- PrimeOperator*

Exactly,

I was hoping I could just put the tool in the "Contents/MacOS" folder of the app, but I can't even get to the point of successfully building the tool because of the "ld" error. I placed a Frameworks folder with a copy of the embedded framework in a location relative to the build location for the tool (i.e. relative to the search path @executable_path/../Frameworks), but this doesn't work. 

I've created faceless Cocoa apps before, so I know this will work, but a faceless Cocoa app has some setup overhead (NSApp and window server stuff). 

--zootbobbalu

----
I think that there is something simple and obvious missing, where the linker isn't finding your frameworks (incorrect framework search paths, bad names, etc.?), but I don't know what it is. I wouldn't spend a lot of time pondering deep issues, but just go back and make sure all of the simple stuff is right. Sorry I can't give anything more specific or helpful. -- PrimeOperator

----

I am having a similar problem with one of my projects: one application, several embedded frameworks. No problem with the frameworks using each other, or the application using the frameworks. But there doesn't seem to be any clean way for a plugin to refer to symbols in the frameworks (an equivalent, if you will of     -bundle_loader for frameworks.

The only thing I can suggest is, if you're sure that all the external symbols referenced by your worker thread will actually be resolvable at runtime, you could try adding a linker flag to your XCode target, something like     -undefined dynamic_lookup or     -bind_at_load, to shut the linker up. Or just weak-link to the framework.

----

It's not as pretty, but load the framework at runtime instead. Load the framework just like any bundle, as in IncludingFrameworksInBundles. --TheoHultberg

----

OK, I figured out how to get Xcode to compile and link a Cocoa Shell Tool to an embedded Cocoa Framework. You only have to make the shell tool a direct dependency of an application that embeds the Framework. You can't build the shell tool directory by setting the shell tool as the active target because you will still get all of the     ld warnings discussed above. The easiest way around this is to create a generic Cocoa application target solely for the purposes of building executables that wish to link against the embedded framework. Once you have a generic app, just add the tools as direct dependencies to the app so when you build the app the tools will build along with it. You can run the tools from any instantiated custom class object created in IB using NSTask.

--zootbobbalu

