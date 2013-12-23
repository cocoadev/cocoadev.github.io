---
layout: page
title: AddressBookTextField
---

How does one do an expanding text field like the names in AddressBook.app? I had thought at first that this was a custom class by Apple, but Delicious Library has a text field that works exactly the same way? Is there an API for this, or do I (as DL might have had to) have to roll my own?

----

You have to roll your own. Have a look at AddressBookCardView. Whoops, nevermind. This isn't what you wanted, but I'll leave it here anyway ;)

Maybe you want a DynamicallyResizingTextViewOrField ?

----

That looks like it. Haven't tested it yet, but thanks!

----

See CCDGrowingTextField :)

----

I've made use of CCDGrowingTextField, but can't for the life of me get the textfield to work like the AB/iCal/Delicious Library ones, much less embed many of them in a view. 

Does anyone have any sample code or really strong pointers for an address-book style view like this?

----
How about being less vague? What about your text field, **specifically** isn't working the way you want it to? Nobody here can read minds and most don't care to do a comparative analysis of several different controls in different applications to figure out what you're talking about. You may want to read HowToAskQuestions and follow those guidelines for asking a question people are willing / able to answer.

Please don't take this as flaming / trolling - people are trying to help you but you have to give us more than vague references. Do as much of the foot work as possible yourself; it's not our responsibility to research and fill in the blanks for you.
----
Are you looking to make them "pop out" like they do in Address Book? Should be easy to do with a child window.

----

OK, the point is well made about being a poor question. Yes, I do mean the "pop-out", sort of hovering. I'll read up on child windows.

Basically I'm after guides on how to get an NSTextField subclass to look like the ones in the apps I mentioned. I've got the resizing to fit contents down nicely, but need help on how to draw the text field in that style. The style is as follows:
Not selected: Just draw text. No background or borders
Selected: Draw text, and a coloured bounding box with shadow around the box

I suppose for drawing the box a bezier path of some description would be the way forward, but have no idea where to start with adding a shadow. If more info is required, please ask.

----
Sorry, I wasn't trying to drive the previous poster's point. Just making an educated guess, anyway..

I would make a BorderlessWindow (/an NSWindow subclass) with a method like     +popOut:(id)control. This will instantiate your BorderlessWindow, move it to the desired location on-screen and then place the control in itself. This should give you the general idea....

    
+ (id)popOut:(id)control
{
      NSRect frame = [control frame];
        frame.origin = control window] convertBaseToScreen:frame.origin];

[[PopOutWindow *window = [[PopOutWindow alloc] initWithContentRect:frame styleMask:NSBorderlessWindowMask backing:NSBackingStoreBuffered defer:NO];
     [window setReleasedWhenClosed:YES]; // should balance the above, assuming this gets closed at some point.

     control window] addChildWindow:window ordered:[[NSWindowAbove];

// put your control in the pop out window here

            [window orderFront:nil];

return window;
}

- (BOOL)canBecomeKeyWindow { return YES; }

- (void)mySpecialClose
{
 //put the control back in its original window
   [self close];
}


----
I'm not sure a window is necessary. The "pop-out" can be accomplished by making the box's frame slightly larger than where the text will go (large enough to encompass a nice thin black border and shadow). When the text field has focus, you'll draw it in the 'pop-out' style (black frame plus shadow effect). When it doesn't have focus, you'll draw just its content text. To accomplish this, you'll need to read over the "Drawing and Views Programming Guide" here: http://developer.apple.com/documentation/Cocoa/Conceptual/DrawViews/index.html ... for drawing the shadow effect, consult the NSShadow documentation. Basically, while drawing you -saveGraphicsState, set a shadow the same way you set a color, draw the shadowed items, then -restoreGraphicsState.
----

OK, thanks for the pointers there. I'll start with the basic drawing method first, as that sounds like it might do the job nicely, but if it doesn't then I'll move onto the BorderlessWindow method.

Thanks again!

