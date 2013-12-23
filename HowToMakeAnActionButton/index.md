---
layout: page
title: HowToMakeAnActionButton
---

I'm trying to make an action button similarly to the one in the Finder and Mail, and am completely lost on what I need to subclass for NSPopUpButtonCell. I had a working button that was just a subclass of NSButton and it had it's own NSPopUpButtonCell to show the menu, but that method wasn't working smoothly. Could anyone point me in the direction I need to go with using an NSPopUpButtonCell and NSPopUpButton? I'm very confused at this point. Thanks.

----

The trick is to subclass NSPopUpButton (to get the menu popping behavior), and a NSPopUpButtonCell, and in that class make a private NSButtonCell and use it for drawing.  Check out http://www.cocoabuilder.com/archive/message/2005/2/1/127252

----

----
Using the example on this page, I've created DKActionButton (http://mabblog.com/blog/?p=142) a interface element that you can easily use in your app to create an action button. I've also posted an (action)gear icon that you can use with DKActionButton.

-MichaelBianco
----

Ok thanks. Here's what I have so far, but when I add a menu item (testing internally for now) the first one gets cut off, and I'm assuming because it's wanting to use that as the title? How do I turn that off? And how do I set the menu so it's flush against the left side of the button? Thanks!

    #import "P2GActionCell.h"

@implementation P2GActionCell

- (id)initTextCell:(NSString *)stringValue pullsDown:(BOOL)pullDown
{
	if (self = [super initTextCell:stringValue pullsDown:YES])
	{
		actionImage = // image
		
		buttonCell = [[NSButtonCell alloc] initImageCell:actionImage];
		
		[self setPullsDown:YES];
		[self setArrowPosition:NSPopUpNoArrow];
		[self setBordered:NO];
		[self setHighlightsBy:NSChangeGrayCellMask];
		[self setUsesItemFromMenu:NO];
		
		[buttonCell setButtonType:NSPushOnPushOffButton];
		[buttonCell setImagePosition:NSImageOnly];
		[buttonCell setImageDimsWhenDisabled:YES];
		[buttonCell setBordered:NO];
		
		[self addItemWithTitle:@""];
		[self addItemWithTitle:@"TEST2"];
		
		[self synchronizeTitleAndSelectedItem];
	}
	
	return self;
}

- (void)dealloc
{
	[buttonCell release];
	[actionImage release];
	
	[super dealloc];
}

- (void)drawWithFrame:(NSRect)cellFrame inView:(NSView *)controlView
{
	[buttonCell drawWithFrame:cellFrame inView:controlView];
}

- (void)highlight:(BOOL)flag withFrame:(NSRect)cellFrame inView:(NSView *)controlView
{
	[buttonCell highlight:flag withFrame:cellFrame inView:controlView];
	[super highlight:flag withFrame:cellFrame inView:controlView];
}

@end

----

I pretty much got it to how I want now. I had to also subclass NSPopUpButton, and to be able to use it in Interface Builder, I had to add the     initWithCoder: method too. It works well now. Just set the action in IB and it works the same as a normal pull-down popup button:
    
@implementation P2GActionButton

- (id)initWithFrame:(NSRect)frameRect
{
	if (self = [super initWithFrame:frameRect])
	{
		[self setPullsDown:YES];
	}
	
	return self;
}

+ (Class)cellClass
{
	return [P2GActionCell class];
}

- (id)initWithCoder:(NSCoder *)decoder
{
	if (self = [super initWithCoder:decoder])
	{
		if (!self cell] isKindOfClass:[P2GActionCell class)
		{
			P2GActionCell *cell = P2GActionCell alloc] initTextCell:@"" pullsDown:YES];
			[self setCell:cell];
			[cell release];
		}
	}

	return self;
}

@end
----
Apple's HIG page says that popup buttons can already have images. Did you try dragging the image onto the button itself in Interface Builder? See http://developer.apple.com/documentation/[[UserExperience/Conceptual/OSXHIGuidelines/XHIGControls/chapter_10_section_3.html#//apple_ref/doc/uid/TP30000359/TPXREF132 for Apple's "screenshot", though this could easily be mocked up, done with a private API, or taken from a running application. Your earlier problem (with the first item getting cut off) is because you didn't switch the mode to "Pull Down" (the default is "Pop Up") in Interface Builder. NOTE: I haven't actually tested any of this (my computer is totally haywire right now), but it seems like it shouldn't be this hard. But then again, look at toolbars. --JediKnil

*You can do it this way, but if the image is smaller (in this instance), the entire button is larger then the image itself. And the button doesn't highlight differently when you click on it like a normal NSPopUpButton does. Regarding the pull down option, it's already setup in my code in both classes.*

All right then, sorry...I retract my statements :). I'll leave my "mistaken" post before just to show people that you don't always have to do this...and as one final comment before someone criticizes me for being too persistent, it would be nice if you used the pop-up button's image...which would make this button more reusable. I bet that changing all references to your     actionButton ivar to     [self image] would allow this to work automagically...because it would use the superclass's image...anyway, I'd like to hear if this works. I'm shutting up now. --JediKnil

The Actionbutton in the Finder is actually a NSSegmentedControl with a single Segment and a NSMenu attached to it. I realized that, when I noticed that the Metal-Buttons generated by the method above aren't blue when they are clicked, whereas the Segmentedcontrols are. If you want to get the look of the Actionbutton in Mail, you still have to Subclass NSPopUpButton and NSPopUpButtonCell, thou.    -- trainee

----

The following lines can safely be deleted from P2GActionCell....

From initTextCell:

    
[self setPullsDown:YES];
[self setArrowPosition:NSPopUpNoArrow];
[self setBordered:NO];
[self setHighlightsBy:NSChangeGrayCellMask];
[self setUsesItemFromMenu:NO];


and in highlight:

    
[super highlight:flag withFrame:cellFrame inView:controlView];


Enjoy. -- rjt

