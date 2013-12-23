---
layout: page
title: CocoaWindowReference
---

Does anyone know how to get a window instance without using interface builder? I have tried creating a new instance using objective -C documentation but have not been successful.

----

The documentation for NSWindow has what you're looking for. Create an instance with the usual methods, release it when done. http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSWindow.html

The discussion in BorderlessWindow shows some techniques, but you can also do those things without setting the borderless window mask flag.

----

The following code is not working for me:

      
int main(){

  NSWindow window;
  struct NSRect;

  [NSWindow alloc];
  rect  = NSRectMake(100,100,50,50);
  window =[NSWindow initWithContentRect: rect: NSTitledWindowMask: NSBackingStoreRetained: NO];
  [window display];	
  [window orderFrontRegardless];
}    

----
You need to set up the AppKit first, and you need to start an event loop after you make the window. Try adding a call to     NSApplicationLoad() before you make the window, and do a     [[NSRunLoop currentRunLoop] run] afterwards.

Oh, and if you're doing this just to see what makes Cocoa tick, fine, but please please please don't make a real application this way. There is no reason to avoid the standard setup with nibs, and it makes life so much easier.

----
That or just     [NSApplication sharedApplication]; works. You are also forgetting to create an autorelease pool, you can't know if any of the methods you call don't use autorelease. EnglaBenny 

----

So I modified the code slightly to use autorelease pools and use NSApplication. But even now it does not seem to work.
Any other ideas?
    

#include <AppKit/Appkit.h>
#include <Cocoa/Cocoa.h>
#include <Foundation/Foundation.h>

int main(int argc, char **args){
	NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];//create an autorelease pool
	[NSApplication sharedApplication];
	NSWindow *window;
        struct NSRect rect= NSRectMake(100,100,50,50);
        [window initWithContentRect: rect: NSTitledWindowMask: NSBackingStoreRetained: NO];
	[window display];	
	[window orderFrontRegardless];
        [window autorelease];   
        [pool release];
        return 0;
}


Thanks.
----

My advice would be to pay more attention to the advice that people are giving you, because you completely missed this part:

*and do a     [[NSRunLoop currentRunLoop] run] afterwards.*

Without that step, your application just quits as soon as it's done setting up the window.

----
That code can't compile. No way it works.
----
Would you care to provide any *details*? Come on, do a little of your own work here.
----
OK, first of all, DON'T use the main method in the AppKit. Ever. You should always leave     main.m the way it is, and do all of your work through classes whenever possible.

*to see why this is, consult Chapter 4 of BookBuildingCocoaApplications; I think the authors were just trying to show how NeXT-studly they are*

The simple code you need is here:
    
NSWindow *window;
NSRect rect = NSMakeRect(100,100,50,50);
window = [[NSWindow alloc]
	initWithContentRect:rect
	styleMask:NSTitledWindowMask
	backing:NSBackingStoreBuffered
	defer:NO];
[window display];	
[window makeKeyAndOrderFront:self];

However, you should put this in the     finishLaunching method of an NSApplication subclass, or the     applicationDidFinishLaunching: method of the application's delegate, if you want it to appear immediately. And you also need to read up on basic Objective C knowledge, such as messaging syntax,     NSMakeRect (not     NSRectMake),     NSRect itself (not a struct),     alloc/    init, what's in the "Cocoa" framework,     #import, and the spelling of "    release." Finally, USE INTERFACE BUILDER! I have not yet come across any cases in *any* program I have used that would work better when the windows are created programmatically, when you could just as easily use a template window. --JediKnil

P.S. Sorry for being so harsh, but you really need to learn about Cocoa before you try relatively advanced stuff like this. Perhaps, like me, you were once a Java or C user, in which everything is done programmatically, and that is the easiest way to do it. Please remember that almost the entire AppKit is built around InterfaceBuilder, instead of it being some tool added later. (It seems, at first, that you cannot even get NSApplication to start without a     .nib file). If you are just trying to figure out Cocoa under the hood, at least use ObjC ideas to do it. Good luck... --JediKnil

------------------------------------------------------------------------------------------

Don't listen to the "you should do things the Approved Way and not fiddle with things you don't understand" crowd.
Some people like the comfort of thinking in a closed box and are usually scared of those who try to look out
the windows

----But on the other hand, don't reinvent the wheel, unless you are a world-class engineer. Some people also like the role of rebel and outcast, but don't have the guns for it.


----I've gotten NSApplication to work fine without a nib file, hell I even have half of my NIB replacement working without any nib files ;) .


------------------------------------------------------------------------------------------

I'm curious as to how to do this as well.  I've got an application with a single window working without a NIB file but I can't seem to add an NSTextField to get some information from the user.  When the program is launched from the Terminal, all text goes to the command line.  Yeah, yeah... use IB and it will be easy.  In this case, my app is being launched from another UNIX process and it doesn't know how to dig down and launch the app properly.  I could write a script that kickstarts the Cocoa app but creating a GUI without a NIB should be doable as well.

DaveW

