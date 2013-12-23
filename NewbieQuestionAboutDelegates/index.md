---
layout: page
title: NewbieQuestionAboutDelegates
---

I'm just now starting to learn Cocoa and I have been reading O'Reilly's Learning Cocoa book.  I am a little confused as to the difference between using conditional statements and delegates to determine if an action should be performed or not.  Are they interchangeable? Are there some times where one is a little better than the other? or are they used in completely separate situations (if so, which situations?)
-GorillaPaws
----

A conditional statement is used to control the flow of execution in a block of code.  Delegate methods are used to provide custom functionality for many situations in the standard Cocoa library as a less complicated (and better engineered) alternative to subclassing.  Their use doesn't really overlap.  To say the same thing with a little less jargon: for the code you write yourself, if you want to do something different based on a condition you can write

if(condition){/*true block*/} else { /*false block*/}

where the else{/*false block*/} is optional

If you want to disallow the main window from closing (for some values of close), you write your own windowShouldClose: handler in a NSObject SubClass like
    
@interface MyWindowController : NSObject
{}
-(BOOL)windowShouldClose:(id)sender;
@end
@implementation MyWindowController
-(BOOL)windowShouldClose:(id)sender
{
    return NO;
}

add the class to your nib file, instantiate it, connect it to your window as delegate and you have a window that doesn't want to close... this is lots cleaner than subclassing NSWindow - not that this is a terribly useful example.


More on that:
At some point, the delegate will be asked to know if the windowShouldClose. The response to that method, as returned by the delegate, IS probably enclosed in an if statement somewhere in NSWindow implementation. The trick here is that YOU DON'T have to write yourself whatever the if is doing. You just decide (through the delegate) what behavior you want.

Of course, all delegate methods are not YES/NO. For example, windowDidClose. When the delegate receives the message, it might want to do something, like changing an element in another window. The delegate is not going to return anything in this case, and the caller NSWindow does not really care what the delegate is doing. Again, this is similar to subclassing, only more flexible way.

----

Er, "windowDidClose" might not be a very good example, since it does not actually exist, as far as I can tell. -WAHa

