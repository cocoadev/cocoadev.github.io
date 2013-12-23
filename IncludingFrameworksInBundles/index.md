---
layout: page
title: IncludingFrameworksInBundles
---

Hello,

I am trying to make an IBPalette to expose some functionality of a framework I have created.  The problem, however, is that the IBPalette can't be linked with the framework because the @executable_path/../Frameworks hack doesn't work if the bundle is not the executable (ie:  with an IBPalette, Interface Builder is the executable).

Any idea on how to make this work?

--JeffDisher

----

It should be possible to load a framework as a bunlde (with     +[NSBundle bundleWithPath:]), but I haven't tested it. --TheoHultberg/Iconara

----

I had been wondering that but I thought it only worked with Mach-O bundles, and not Mach-O dylibs.  I haven't had time to try it yet but I will post back when I get a chance to poke at it.

Thanks,
--JeffDisher

----

I tried this out and it does indeed seem to work perfectly.  Thanks for poking me to try it.

I few gotchas, however:  I had to resort to some hacks to make the bundle link properly (since it wants all links resolved at compile time) - I had to load the framework as a bundle and then ask that NSBundle object for the class with the name of the class that I wanted (then I was able to build instances of my framework's class by using that class variable.  Getting NSBundle to find the framework required some path hard-coding since I wasn't able to get it to find the framework using more obvious mechanisms (although this could just be a bug in my code or a typo, somewhere).

Other than that it does seem to work.  Thanks, Theo!

--JeffDisher

----

More untested hypotheses: shouldn't it be possible to include the framework in your project, and import it's in your classes. If make sure that you load the framework before any other code is run (say in     +load or     +initializeof thebundle's principal class), it should be OK. Try, it can't do much more than crash your computer. You should be able to use class names from the framework, without the compiler bitching about unknown types.

--TheoHultberg/Iconara

----

Well, I already include the framework for its headers.  I can't include it as a required framework, though, since the dynamic link editor seems to require that the framework be present when it loads the bundle.  I will poke around at to see if anything cleaner can be done, though.

--JeffDisher

----

Added Jan 6, 2005:

related question/answer:
http://lists.apple.com/archives/cocoa-dev/2004/Jan/msg01963.html

Jeff: to find your framework in your IBPalette, you can get the path with [[NSBundle bundleForClass:WHATEVER_CLASS_IN_THE_PALETTE_CODE] bundlePath] and then hard-code inside that. This is maybe what you are doing? --CharlesParnot

I am currently looking for the same kind of answer, in a slightly different context. Those tricks don't work. I want to provide a framework C for 3rd parties to develop plug-ins P to a bundle B (also a plug-in) for an application A (no, no, this could happen to you too!!). The framework C is supplied to provide the compiled code of a superclass to subclass in the plug-ins loaded by B. The framework C will be linked at compile time to the plug-in of the 3rd parties developer. I was thinking it is easier to provide framework C with the bundle B, so that the user only needs to install bundle B to also get framework C. Now, what happens at runtime? When application A runs, it loads the plug-in B at some point. Then B may load a plug-in, that will try to load framework C or fail to load. The framework C is inside bundle B, not at @executable/../Frameworks. So I need to build framework C with an absolute path. That absolute path should be the path that gets inside bundle B. However, because bundle B is a plug-in, it may be in different locations, /Library/Plugins or ~/Library/Plugins. So what INSTALL_PATH should I use, or what trick should I use without giving up the possibility of (i) have framework C inside bundle B, (ii) simply provide framework C to the 3rd party developer because we are all used to that.

One of the thing I tried is to force the loading of framework c when bundle B is loaded (using code), but then the plug-in P fails to load because it wants to load the same framework in another location (it does succeed to load when the framework c is in /Library/Frameworks, and then I have two versions of the same framework loaded at the same time!! I did not know it was possible, I though the runtime would be smart enough to realize there was already a framework with the same identifier and version number loaded).

Another possibility for me is to answer the question: can INSTALL_PATH hold several values instead of just one?

Sorry the problem is a bit peculiar, but an answer to it might solve elegantly all the similar situations evoked in this page. --CharlesParnot

*I don't understand the problem... let plugin B load the framework once, when it (B) is loaded. It will then be available to all subplugins (P) loaded by B. You don't have to load it once for every subplugin, they all share the symbol space. Once it's loaded it will stay loaded, until A is quit. --Theo*

----

Don't forget to check if the framework you want to load is already loaded, since loading a framework twice may (will) result in your host application crashing and burning. Another developer might have had a similar idea to you, and decided to include the same framework as you. However, you can't check if the framework is loaded by using NSBundle's     -isLoaded, since the framework might be loaded from another location (another copy of it has been loaded). This is one way of checking, which works for Objective-C frameworks:

    
if ( ! NSClassFromString(@"NameOfClassFromFramework") ) {
    // ok, didn't find the class, it's probably safe to load the framework
    // load the framework
}


If anyone has a suggestion for checking if a framework with non-Objective-C symbols is loaded, I'm curious how to do it.

--TheoHultberg/Iconara

*How about this:*
    
+ (BOOL)frameworkIsLoaded:(NSString *)frameworkBundleIdentifer
{
    BOOL isLoaded = NO;

    NSBundle *frameworkBundle = [NSBundle bundleWithIdentifier:frameworkBundleIdentifer];
    if (frameworkBundle != nil)
    {
        isLoaded = [frameworkBundle isLoaded];
    }

    return isLoaded;
}


----

Well, in Tiger you could use **@loader_path** -- DevinBayer

In Mac OS X 10.4, we now support LC_LOAD_DYLIB paths that start with @loader_path/ which means relative to the image containing the LC_LOAD_DYLIB command. This enables the construction of bundles and other "directory trees" of final linked images which can be installed anywhere. 
Currently, ld(1) has no options for directly embedding @loader_path into LC_LOAD_DYLIB load command. Instead, you must post-process your final linked images with install_name_tool.

----
[ replying to Seb's post, which was updated and moved below ]
Ummm, actually, you should probably be using a Copy To build phase with a destination of "Shared Frameworks" (or just "Frameworks," if you want to keep it private). Try using     bundle sharedFrameworksPath] stringByAppendingPathComponent:@"*frameworkName*.framework"] --[[JediKnil

----
True, updated version follows:

* Add the framework to your project, but add it as a Resource.
* Create a new Copy Files phase, select Frameworks for destination, and drag the framework under this new phase to add it
* #import the framework's header files as you normally would
* in your bundle's +load function, add:

    
	if(!NSClassFromString(@"*class name from framework*"))
	{
		// note slash in string!
		NSString *frameworkPath = [[[NSBundle bundleForClass:[self class]] privateFrameworksPath] stringByAppendingPathComponent:@"/*FrameworkName*.framework"];
		NSBundle *Framework=[NSBundle bundleWithPath:frameworkPath];
		[Framework load];
	}

This allows to load a framework dynamically from a bundle, without having to recompile the framework to fix or worry about the pesky @executable_path problem.
The above code assumes the standard Frameworks directory is used; modify the destination in the copy phase and method used to retrieve the directory as needed.

- Seb
----

