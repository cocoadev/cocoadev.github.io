---
layout: page
title: TestForKeyDownOnLaunch
---



I can not figure out how to get my program to pick up a key down event on launch. Ideally, I want to be able to test to see if a key is down at any given time.  I have been overriding General/NSResponders keyDown: (General/NSEvent *) event method from within in a window. I think I tried doing it in General/NSApp too. I don't know. If you have not been able to figure it out, I am rather new to cocoa--and make no attempt at hiding my newbieness. I can not find any useful documentation/tutorials. Everything in Apple's is "forthcoming".

So anyway, if anyone knows how I can get my application to do something different if launched with the option key held down than when it's not, please let me know. Thanks!

gorman

----

I have been told that this is as close to a flaw in the General/AppKit API you can get. It seems to be impossible to get modifiers or keys without having access to an General/NSEvent.

----

However, you can use Carbon calls to do it. Here is a nice category for finding the status of a few modifiers (Though you should be aware that holding down the alt key during app startup would under General/NeXT start another instance of the program. If apple chooses to revive this behaviour your app will be quite inconsistent):
    
@interface General/NSEvent (General/ModifierKeys)
+ (BOOL) isControlKeyDown;
+ (BOOL) isOptionKeyDown;
+ (BOOL) isCommandKeyDown;
+ (BOOL) isShiftKeyDown;
@end

@implementation General/NSEvent (General/ModifierKeys)

+ (BOOL) isControlKeyDown
{
    return (General/GetCurrentKeyModifiers() & controlKey) != 0;
}

+ (BOOL) isOptionKeyDown
{
    return (General/GetCurrentKeyModifiers() & optionKey) != 0;
}

+ (BOOL) isCommandKeyDown
{
    return (General/GetCurrentKeyModifiers() & cmdKey) != 0;
}

+ (BOOL) isShiftKeyDown
{
    return (General/GetCurrentKeyModifiers() & shiftKey) != 0;
}

@end


----


Well I have no clue to how to implement that code you just gave me (accesssing carbon libraries from a cocoa app).
 I tried for at least an hour. But It did give me another idea. Couldn't I overide General/NSApplications did finish launching method and make it generate a event,
 and then test to see if a moifier key was down during that event (using General/NSEvent's modifierFlags function)? I tried to do this, 
but I do not think i am subclassing General/NSApplication correctly......I have my custom class set  as the principal class.
 But when I put a breakpoint inside any of the overidden functions and debug it, it never breaks.

Any insight would be greatly apreciated, and thanks for the already provided help.

----

It is very simple to call into Carbon from Cocoa. Just include the framework Carbon.framework in your project and call into it! In the source code file, #import <Carbon/Carbon.h>

-- General/DavidRemahl

----

Thanks, I thought all I had to do was the #import! Got it working, thanks for the code snippet!

----

One Question:
How can I check the status of the Boolean in the .m file?

-- General/MartinWeil

----

You can quite easily get access to an event by doing General/[NSEvent currentEvent] and then you can binary or against the modifierflags.

-- General/FinlayDobbie

----

I have no idea what you mean by, could you give me a code snippet?

-- General/MartinWeil

----

Finlay..I don't think +currentEvent is valid during application launch, only while in the processing of an event.

----

So, what must I do for checking it? I don't know how long I have tried, maybe ten hours? I've searched everything I found but I didn't get it working.

----

Use:
    
if(General/[NSEvent isControlKeyDown])
{
   // do code
}


----
On 10.6, you can use 
    
General/[NSEvent modifierFlags]

