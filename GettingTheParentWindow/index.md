---
layout: page
title: GettingTheParentWindow
---

Hi there,

I'm doing an application which opens two windows(nib based), Window A and Window B... Window B is opened from Window A, i.e. a button inside Window A! My question is now:

How do I get the parent window (Window A) from some function inside Window B, i.e. in Window B I want to display the title of Window A?


/Mads
----
Well you could have them connected via IB or otherwise ie subclass NSWindow and use a custum show method that would take the calling window as a argument and store it like this:
    
#import <Cocoa/Cocoa.h>

@interface CustomWindow : NSWindow
{
   NSWindow *callingWindow;
}
-(IBAction)showWindowAndStoreParent:(id)sender;
@end


#import "CustomWindow.h"

@implementation CustomWindow

-(IBAction)showWindowAndStoreParent:(id)sender
{
   [self makeKeyAndOrderFront:sender];

   [callingWindow release];

   callingWindow = [sender retain];
}
@end



You could also ask the app's window controller for an array of windows and look for it. 

It is also possible that your program would benifit from the child/parent approach. Look into that first.

GormanChristian

----

that should actually be [[sender window] retain], if the action is being called by a button. *--boredzo*

