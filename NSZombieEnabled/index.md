---
layout: page
title: NSZombieEnabled
---

**NOTE: Consider using Instruments's Zombies template instead!** It gives you more information, particularly on what caused the object to be zombified.

----


NSZombieEnabled is an environment variable which controls whether the Foundation runtime will use zombies. When zombies are enabled, a deallocated object's class is dynamically changed to be     _NSZombie, and by default, the memory region is never marked as free, although this can be controlled separately. 
The end result is that, with zombies enabled, messages to deallocated objects will no longer behave strangely or crash in difficult-to-understand ways, but will instead log a message and die in a predictable and debugger-breakpointable way. This is *the* tool to use when trying to track down over-releases and premature releases.

Zombies will take effect for all Objective-C objects that are deallocated through normal means, including most Cocoa classes as well as user-created classes. On 10.4 and earlier, a rather important exception to this is most/all TollFreeBridged classes, as they are deallocated using CoreFoundation which NSZombieEnabled will not trap, in which case CFZombie must be used. On 10.5, NSZombieEnabled will work for TollFreeBridged classes as well.

See the CFZombie page for information on how to debug errors involving TollFreeBridged classes such as NSCFString. 

For more information on this and other helpful debugging aids read 

    /System/Library/Frameworks/Foundation.framework/Headers/NSDebug.h

**Use in Xcode:**

*Double-click an executable in the Executables group of your Xcode project.
*Click the Arguments tab.
*In the "Variables to be set in the environment:" section, make a variable called "NSZombieEnabled" and set its value to "YES".


NSZombieEnabled should not be left in place permanently, as by default no objects will ever be truly deallocated, making your application use tremendous amounts of memory. If you find yourself needing to use it often, you can create a second executable in your project which uses NSZombieEnabled so that you can easily switch between them. Do this by right-clicking the original executable, selecting "Duplicate", and then setting up NSZombie in the new executable.

Or, you can not use Xcode and instead run your application from the terminal, with the command:
    env NSZombieEnabled=YES Whatever.app/Contents/MacOS/Whatever
Which will set the variable and run your application.  (Substitue in your app's name for "Whatever", of course)

----
	
If you use this from time to time, you might want to add a check someplace like applicationDidFinishLaunching, to alert you via the log whenever this option is on. That way you will avoid getting egg all over your face like I did, after spending hours trying to find my memory leaks :-)

    
	if(
		getenv("NSZombieEnabled") || getenv("NSAutoreleaseFreedObjectCheckEnabled")
	) {
		NSLog(@"NSZombieEnabled/NSAutoreleaseFreedObjectCheckEnabled enabled!");
	}


Here is a list of break points to put in ~/.gdbinit that are really helpful in debugging memory problems:

    
 fb -[NSException raise]
 fb -[NSAssertionHandler handleFailureInFunction:file:lineNumber:description:]
 fb -[NSAssertionHandler handleFailureInMethod:object:file:lineNumber:description:]
 
 #define NSZombies
 # this will give you help messages.  Set to NO to turn them off.
 set env MallocHelp=YES
 # might also be set in launch arguments.
 set env NSZombieEnabled=YES
 set env NSDeallocateZombies=NO
 set env MallocCheckHeapEach=100000
 set env MallocCheckHeapStart=100000
 set env MallocScribble=YES
 set env MallocGuardEdges=YES
 set env MallocCheckHeapAbort=1
 
 set env CFZombie 5
 
 fb -[_NSZombie init]
 fb -[_NSZombie retainCount]
 fb -[_NSZombie retain]
 fb -[_NSZombie release]
 fb -[_NSZombie autorelease]
 fb -[_NSZombie methodSignatureForSelector:]
 fb -[_NSZombie respondsToSelector:]
 fb -[_NSZombie forwardInvocation:]
 fb -[_NSZombie class]
 fb -[_NSZombie dealloc]
 
 fb szone_error

-- AdhamhFindlay

