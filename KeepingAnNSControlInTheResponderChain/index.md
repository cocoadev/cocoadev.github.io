---
layout: page
title: KeepingAnNSControlInTheResponderChain
---



Hello everyone!

I've been banging my head against a wall trying to solve this for the past day and a half, so now I turn to the gurus at CocoaDev for advice. I am implementing a canvas (an NSControl subclass) that keeps track of the currently selected tool (for modifying the image in the control), and I want to implement hot keys to switch between the tools. I want it to automatically choose one tool when the command key is held down, another when the option key, and a third with the control key.

I am using flagsChanged: to monitor for these key presses and everything works beautifully when the control is the key view, but when it is not, I can't trap the message. How can I get around this?

I guess my initial impulse is to find a way to keep my view in the responder chain at all times, but I'm really not sure how to do this. Any suggestions would be greatly appreciated. Thanks!

--EliotSimcoe

---- 

I'm kinda confused what you're trying to do.  Do you want:

*to change your canvas's tool whenever someone hits a modifier key?  This doesn't sound practical.  It seems like it would be very confusing to hit, for example, command-S to save and find that one's tool had been changed in the proces.  
*to differentiate between control-clicks, command-clicks, option-clicks and plain clicks and change the tool on the first three?  In this case, you just need to check the result of     [theEvent modifierFlags] in your -mouseDown: method to see which kind of click you have and behave accordingly.
*to have some other trigger to go along with your modifier keys?  Please elaborate.

Manually playing with the responder chain is usually a bad idea. Keep in mind that your key window's delegate, your main window's delegate and your application's delegate are all included in the responder chain and will be given a chance to respond to unclaimed events, so you can put responder methods in one of those if you need them called regardless of the current first responder.  -- Bo

----

Well, think of it kind of like Photoshop, where you have a canvas, and tools that operate on the image in the canvas. When you hold down the command key and are hovering over the canvas, the "move" tool is automatically selected. When the command key is released, the tool changes back to what it was before the "hot switch". This is the effect I am going for. Consider however, that the tools are also managed by the view. I guess the paradigm is similar to that of NSToolbar, but that is irrellivent for this discussion. Anyway, that is the behavior I am trying to implement. The only issue I am having, is that I don't receive notification that the modifier flags have changed unless I am key view.

-- EliotSimcoe

----

Ok, I'm pretty sure I know what you're talking about now.  First, Cocoa has cursor management functions that let you change the cursor based on what view the mouse is over, but they're not modifier key aware, so you'll have to manually reset them in response to modifier key change.  For example:
    
// implementing this function will tell your app what you want the cursor
// to be when it's over your view.
@implementation MYCursorChangingView
- (void)resetCursorRects
{
	unsigned int currentFlags = 
			[[[NSApplication sharedApplication] currentEvent] modifierFlags];
	// these are just placeholders
	// your function will probably be a bit more complex ;)
	if (currentFlags & NSShiftKeyMask) {
		viewCursor = [NSCursor IBeamCursor];
	} else if (currentFlags & NSAlternateKeyMask) {
		viewCursor = [NSCursor crosshairCursor];
	} else if (currentFlags & NSCommandKeyMask) {
		viewCursor = [NSCursor openHandCursor];
	} else {
		// this is for no modifiers
		viewCursor = [NSCursor pointingHandCursor];
	}
	[self addCursorRect:[self bounds] cursor:viewCursor];
}
@end

// this tells the window to recalculate the cursor when the modifier keys change.
// By putting it in the window delegate, you assure it will get called as long as
// the window is frontmost, which is how most Mac apps seem to handle cursor
// changes. The window's -resetCursorRects will automatically call your view's 
// -resetCursorRects method
@implementation MYWindowControllerWhichMustAlsoBeSetToWindowsDelegate
- (void)flagsChanged:(NSEvent*)inEvent
{
	self window] resetCursorRects];
}
// this is needed too because the modifiers can change 
// when it's not main without it being told
- (void)windowDidBecomeMain:([[NSNotification*)inNot
{
	self window] resetCursorRects];
}	
@end

Now, you figure out the tool based on the modifier keys only when they click, which would look something like this:
    
@implementation [[MYCursorChangingView
- (void)mouseDown:(NSEvent*)inEvent
{
	unsigned int currentFlags = [inEvent modifierFlags];
	if (currentFlags & NSShiftKeyMask) {
		// this is all just placeholder code for whatever 
		// you'd really be doing to set the tool
		[self setTool:[MYToolChest textTypingTool]];
	} else if (currentFlags & NSAlternateKeyMask) {
		[self setTool:[MYToolChest selectAreaTool]];
	} else if (currentFlags & NSCommandKeyMask) {
		[self setTool:[MYToolChest dragObjectTool]];
	} else {
		// this is for no modifiers
		[self setTool:[MYToolChest currentTool]];
	}
	// and then do whatever drawing your tool does here
	// or in -mouseDragged: or -mouseUp:
}
@end

That's my idea at least; you may find it easier to change the tool in your view's -resetCursorRects method instead of -mouseDown:, which should work too.  -- Bo

----

Here is the gatcha... I can't enforce that the window controller reset the cursor rects when the flags change. It needs to be done from the view. That's why I need a way to keep the view in the responder chain at all times. I pass along the event even though I use it, because I don't want to block it from other classes, so I won't be an interruption, I just need to filter for it at all times. The reason for this is I am writing a framework in which my view will be distributed. I don't want to require the user of the framework to make special considerations for this feature.

P.S. The tool is changed in the flagsChanged: method...

-- EliotSimcoe

----

Uh, ok.  That's a bit more complicated.  This might be the incantation you're looking for:
    
- (void)awakeFromNib
{
	[self setNextResponder:self window] nextResponder;
	self window] setNextResponder:self];
}

There are some caveats however.  The biggest one is: Don't let yourself become first responder if you're going to use this.  You'll short-circuit the window and your superviews right out of the responder chain.  Getting around this would require you to override the     -becomeFirstResponder and     -resignFirstResponder methods and fix the responder chain manually, which is, alas, outside the scope of this comment.  I'd still recommend using cursor rects to change the cursor rather than manually changing it; just call     [[self window] invalidateCursorRectsForView:self]; in your     -flagsChanged: method.  -- Bo

PS Oh yes, I said caveats not caveat.  The other big one is that -awakeFromNib won't get called if the view is created programmatically instead of through IB.  Putting the same code in -viewDidMoveToWindow should cover that possibility.  Also, if your view is ever dealloc'ed separately from the window, you'll have to include some code in -dealloc to remove it from the responder chain or you'll inevitably get a bus error.

----

Okay! Here is what I have implemented and it seems to work well.

    
#if 0
#pragma mark Deallocating views
#endif
- (void)dealloc
{
    [self removeFromResponderChainOfObject:[self window;
    
    [super dealloc];
}

#if 0
#pragma mark
#pragma mark Modifying the responder chain
#endif
- (void)addToResponderChainOfObject:(NSResponder *)anObject
{
    id responder = anObject;
    id nextResponder = nil;
    
    // perminantly add myself to the responder chain
    while( nextResponder = [responder nextResponder] )
    {
        responder = nextResponder;
    }
    
    [responder setNextResponder:self];
}

- (void)removeFromResponderChainOfObject:(NSResponder *)anObject
{
    id responder = anObject;
    id nextResponder = nil;
    
    // remove myself from the current responder chain
    // I have added myself to
    while( nextResponder = [responder nextResponder] )
    {
        if( [self isEqual:nextResponder] )
        {
            [responder setNextResponder:nil];
            break;
        }
        
        responder = nextResponder;
    }
}

#if 0
#pragma mark
#pragma mark Event processing
#endif
- (BOOL)becomeFirstResponder
{
    if( [super becomeFirstResponder] )
    {
        [self removeFromResponderChainOfObject:[self window]];
        
        return YES;
    }
    
    return NO;
}

- (BOOL)resignFirstResponder
{
    if( [super resignFirstResponder] )
    {
        [self addToResponderChainOfObject:[self window]];
        
        return YES;
    }
    
    return NO;
}

#if 0
#pragma mark
#pragma mark Managing the view hierarchy
#endif
- (void)viewWillMoveToWindow:(NSWindow *)newWindow
{
    [self removeFromResponderChainOfObject:[self window]];
    [self addToResponderChainOfObject:newWindow];
    
    [super viewWillMoveToWindow:newWindow];
}


Can you find anything illegal in my implementation? I tried what you suggested, inserting the view as NSWindow's resonder, but it caused IB to crash. I think this is better anyway, because now the view can keep its next responder, where before it would have been lost.

-- EliotSimcoe

----

It caused InterfaceBuilder to crash?  That's odd, since I don't remember suggesting changing anything in IB.  Anyway, this code looks like it sets up a loop in the responder chain in -addToResponderChainOfObject:, since your view's starting nextResponder (which you make a point of not changing) will invariably point back to the window in a couple of steps, where it'll point back to your view in a couple more steps, etc.  Unless there's something I'm not seeing here, I'd guess that what's actually happening is your -addToResponderChainOfObject: method is never getting called because your view is never resigning first responder and never changing windows, and your code is only working because your view is always the firstResponder.  -- Bo

----

What's so odd about it? It is an NSControl subclass which happens to have an IB palette created for ease of use. This IB palette loads the class into IB when it is loaded, and my view caused a crash when first responder changed in IB (Before my modifications noted above).

My class is NOT always first responder. I have an NSTableView which manages some information about the image displayed in my class, it is becomes first responder as necessary and behaves normally.

-addToResponderChainOfObject: is in fact getting called every time I resign first responder, but you are correct that if a message was passed down the responder chain it created a loop. I hadn't noticed this at first, so thank you for your insight. I have made the following modification to my method and things seem to work well now (though they SEEMED to work well before, so this doesn't voice confidence).

    
- (void)addToResponderChainOfObject:(id)anObject
{
    id responder = anObject;
    id nextResponder = nil;
    
    // perminantly add myself to the responder chain
    while( nextResponder = [responder nextResponder] )
    {
        responder = nextResponder;
    }
    
    [responder setNextResponder:self];

    // this is new
    [self setNextResponder:nil];
}


I don't know what warranted the hostile remark about your not having suggested changing IB, but I appologize if I have offended you.
-- EliotSimcoe

----

Sorry.  I didn't think I was acting hostile, only confused.  Keep in mind that if you set your next responder to nil in -addToResponderChainOfObject:, you have to restore it when you become first responder or you won't pass on events, for example     [self setNextResponder:[self superview]]; -- Bo

