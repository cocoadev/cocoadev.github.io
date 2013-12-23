---
layout: page
title: NSBundle
---

Documentation at AppleComputer:

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSBundle_AppKitAdditions/Reference/Reference.html

----

**Notes: **

NSBundle is a lovely mechanism for abstracting a directory of loadable code and/or resources. It's used to represent file formats, plug-in formats, and even full applications. Among other features it manages loading plug-in code, provides a scalable mechanism for localizing any resource set, and abstracts the process for looking for resources within your application. 

NSBundle is largely defined by FoundationKit. AppKit extends NSBundle to load NIB files.

----

**Be careful with bindings**

If loadNibNamed:owner: is used each binding to FilesOwner will retain owner. The retain count will go up and it will not be possible to release owner as you would expect. We also have the pain of releasing all the top level objects. A subclass of NSWindowController is probably a better choise even if we are not interested in a window. I see the topic is not new, ReleasingNSWindowControllerWhenWindowCloses. This notice could be deleted perhaps.

JonathanJansson

----

**Dynamic Linking of Classes**

I today discovered that my Plug-in and the program loading my plugin both contained object code for the same classes.  That is, the main program contains definitions of several classes (MEWebUtils, MEWeatherModule, etc) that are needed by dynamically-loaded plug-ins as well.

When trying to build a Plug-in Target without including these classes, I would receive the following link error:

    
ld: Undefined symbols:
.objc_class_name_MEWebUtils .objc_class_name_MEWeatherModule


To build this target, I added the following option to the linker flags: 

    
-bundle_loader $(BUILT_PRODUCTS_DIR)/path/to/main/executable


Now, the program compiles no problem.  For more information on this consult the man page for ld and this post: http://www.cocoabuilder.com/archive/message/cocoa/2004/7/1/110817

-Joe

----

**Plug-Ins**

A dedicated page on this wiki has been created to talk about those particular bundles: read PlugIns...

----

I'm trying to write an app which saves its data internally, to its bundle.  Here is an example, which is a simple modification of the standard core data stub:

    
- (NSString *)applicationSupportFolder
{
	NSLog(@"Support path: %@", [[NSBundle mainBundle] resourcePath]); //Changed from sharedSupportPath
    NSArray *paths = NSSearchPathForDirectoriesInDomains(NSApplicationSupportDirectory, NSUserDomainMask, YES);
    NSString *basePath = ([paths count] > 0) ? [paths objectAtIndex:0] : NSTemporaryDirectory();
    return [basePath stringByAppendingPathComponent:@"Gestalt"];
}


Not surprisingly, the write seems to not occur.  However, interestingly, the app doesn't raise any errors either.  I'm thinking there is some kind of abstract prohibition against a running app modifying its own bundle during runtime.  In short, I'm trying to write an app which carries its data with it.  Does anyone know how to accomplish this?

----

Your code is highly confusing. You log your bundle's support path, but otherwise you completely ignore it. In the rest of the code, you get the Application Support directory, and then either return the "Gestalt" subfolder of that or of the temporary directory of the call failed. Nowhere are you actually *returning* your bundle's resource path, which would presumably be why it never gets saved there.

However, this is a really bad idea anyway. You have no guarantee that you can save to your application. If your application is on a read-only disk image, a CD, a read-only network share, or even in the standard /Applications directory but running as a non-administrator user, you will not have permission to modify your application bundle. Such behavior is therefore extremely unreliable, and highly nonstandard.

