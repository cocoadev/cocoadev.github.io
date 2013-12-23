---
layout: page
title: BundlesAndPlugIns
---



I'm interested in accepting plugins or extensions or what-ever-you-want-to-call-it in my application. The first one could be an implementation of a console, which should catch all notifications flying between my objects. Could anyone show a simple sample of it? -BobC. 

----

First you would of course use a NSDirectoryEnumerator to find all plug-ins in certain directories, but if for example you had a plugin called Console.plugin at ~/Library/Application Support/My Great App/, you would load it as follows:

 <nowiki>
NSString *plugInPath;
NSBundle *plugInBundle;
Class principalClass;
id newInstance;

plugInPath = [@"~/Library/Application Support/My Great App/Console.plugin"
    stringByExpandingTildeInPath];

plugInBundle = [NSBundle bundleWithPath:plugInPath];

if (principalClass = [plugInBundle principalClass])
{
    newInstance = principalClass alloc] init];
    [newInstance listenForNotifications];
}
</nowiki>

Then just implement a listenForNotifications method in your plug-in. Hope this was what you were after.
-- Ibson

----

In general it is better to use [[NSSearchPathForDirectoriesInDomains to search for one or more Library directories. Find NSSearchPathForDirectoriesInDomains in NSPathUtilities.h. Not sure where the documentation is ... :-/

-- MikeTrent

----

What are people's thoughts on making use of the Core Foundation Plug-in Services from within a Cocoa app? I've seen a few plug-in options discussed and am wondering about the viability of this one. Thoughts?

file://localhost/System/Library/Frameworks/CoreFoundation.framework/Versions/A
/Resources/English.lproj/Documentation/PluginServices/Plug_in_ServicesConcTask/index.html

JoeZobkiw

----

Well, it appears that CFPlugIn provides a way to do dynamic binding (via a Microsoft COM-like mechanism) in non-dynamic languages (C or C++). So I have the following thoughts:


* Objective C is a dynamic language, and it has some great facilities for discovering API at runtime. COM offers nothing to the Objective C programmer; it even adds some overhead to re-implement what Objective C provides for free (or should I say, "for 'free'"). So, in general, CFBundle is a better choice for the Cocoa programmer.

*Because Objective C is dynamic, Objective C bundles cannot be unloaded. (Disbelieve? Download the Objective C runtime from Darwin...) This is a real pain in the backside for applications that might want to unload a plugin.

For example, say I run System Preferences and click on Screen Saver (or Screen Effects, if you're on Jaguar). System Preferences loads in a bunch of screen saver modules, including my own fictional "After Dinner" module. I test "After Dinner" for a while and discover a bug. I fix the bug, re-build, and re-install the module and return to System Preferences. Since "After Dinner"'s objects have already been bound within System Preferences, they won't be bound a second time. This can mean trouble if I changed the size of one of my classes or if I added/removed classes. The only solution is to quit and relaunch System Preferences. This isn't a big deal with System Preferences, but it might be a bigger deal for some kind of server. 

Clearly Objective C isn't appropriate when writing unloadable bundles, and since I want some kind of dynamic binding in my plugins CFPlugIn sounds like the way to go.

*Your Cocoa app may want to share plugins with Carbon apps on 9. Since 9 has no Objective C runtime, you'll need to stick with C or C++ (or I guess Java, but let's not go there). Again, we want dynamic binding, so CFPlugIn sounds like the way to go.

Now, I'm not sure what might happen if you use Objective C in a Carbon app on X. It's entirely possible the Objective C runtime is there and your code will cooperate with the host application's CFRunLoop, etc. But, I'd say "better safe than sorry" and stick with CFPlugIn.


So, there's my thoughts. From a point of view of Cocoa application design, simple bundles of Objective C objects are better than COM objects. But, from the point of view of application implementation, COM objects have some advantages. Personally, I would stick with CFBundle unless one of these other circumstances drove me towards a non-dynamic programming language.

-- MikeTrent

----

Why would one use CFBundle instead of NSBundle? -- PeterMonty

CFBundle has a C API and can be used easily by C/C++ based Carbon applications and UNIX tools. Note that CFBundle doesn't provide any special symbol discovery mechanisms for C/C++ clients ...

-- MikeTrent

There's an article on CocoaDevCentral about plug-ins:
http://cocoadevcentral.com/articles/000068.php

----
Actually, CFBundle and NSBundle are not toll-free bridged. This is from the docs:

"Unlike some other Foundation classes with corresponding Core Foundation names (such as NSString and CFString), NSBundle objects cannot be cast ("toll-free bridged") to CFBundle references."
--Ibson

----

I also think it would be very useful to be able to reload a bundle. I was wondering if this would be possible:


*Create a tool that runs an NSRunLoop
*setup the tool to act as a server using NSConnections
*have the server load a bundle and serve objects from the bundle to its proxy


I don't know how much of a hack this would be, but it seems like this can work. Whenever you want to reload the bundle, just terminate the task and relaunch a new one.

