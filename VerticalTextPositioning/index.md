---
layout: page
title: VerticalTextPositioning
---

I have searched everywhere and found little information on positioning text vertically. I have a General/NSButton as an example. How could I position the text at the top of the button (vertically) as opposed to the center? I am using an Attributed String to position it horizontally, but have found no similar attribute for vertical positioning. Any help or pointers in the right direction. I have a feeling I am going to need to subclass General/NSButton and do it myself. Has anyone done this before?

----

There is no mechanism for setting the vertical position of text in an General/NSButton. You will have to subclass General/NSButtonCell, since it is what's actually responsible for drawing the button. Note also that this is not standard Mac OS X UI. You may have a very, very good reason for changing the standard appearance of one of the most basic controls, but if you *don't* then you really shouldn't change it. Many users (myself included) shy away (read "run away screaming") from foreign, clunky-looking applications with strange buttons. Makes them wonder what else doesn't work as expected.

----

Thanks, General/NSButtonCell was an alternative. Has anyone done vertical spacing on a General/NSButtonCell before, and if so are there examples? I did some searching around and found people talking about it, but not much more. Also, yep, I have a good reason for wanting to change it :P... A very good reason dealing with an animation and enlarging a button and moving the text up.

----

It's not "an alternative" ... it's the only way you can accomplish your goal.

----
Yes, I know, Sorry, I meant that I was holding General/NSButtonCell as an alternative if I could not do what I wanted to with General/NSButton; which as stated above, would seem impossible. Therefore I will be using General/NSButtonCell... I will post my code for it here when I am done as there seem to be no examples of this in case anyone else wants to use it.

----

As promised, here is some code for a General/NSButtonCellEnhanced.... Note it might not compile, but the overall code does work. I just had to strip out other parts as my cell does a lot more then just move text around :)

General/NSButtonCellEnhanced.h
    
#import <Cocoa/Cocoa.h>
typedef enum {
	General/TopLeft,
	General/TopRight,
	General/TopCenter,
	General/BottomLeft,
	General/BottomRight,
	General/BottomCenter,
	General/LeftCenter,
	General/RightCenter,
	Center
} General/IMBTextPosition;

@interface General/NSButtonCellEnhanced : General/NSButtonCell {
	General/NSMutableDictionary *displayText;
}
- (void)setAttributedTitle:(General/NSAttributedString *)aString;
- (void)setAttributedTitle:(General/NSAttributedString *)aString atLocation:(General/IMBTextPosition)location;
- (void)setAttributedTitles:(General/NSDictionary *)allStrings;
- (void)removeAttributedTitle:(General/NSAttributedString *)aString atLocation:(General/IMBTextPosition)location;
- (void)clearAttributedTitles;
- (General/NSDictionary *)getAttributedTitles;
@end


    
#import "General/NSButtonCellEnhanced.h"


@implementation General/NSButtonCellEnhanced
-init {
	if(self = [super init]) {
		displayText = General/[[NSMutableDictionary alloc] init];
	}
	return self;	
}
-(void)dealloc {
	[displayText release];
	[super dealloc];
}

- (void)setAttributedTitle:(General/NSAttributedString *)aString {
	[self setAttributedTitle:aString atLocation:Center];
}

- (void)setAttributedTitle:(General/NSAttributedString *)aString atLocation:(General/IMBTextPosition)location {
	[displayText setObject:aString forKey:General/[NSNumber numberWithInt:location]];	
}
- (void)setAttributedTitles:(General/NSDictionary *)allStrings {
	[displayText release];
	displayText = [allStrings retain];
}
- (void)removeAttributedTitle:(General/NSAttributedString *)aString atLocation:(General/IMBTextPosition)location {
	[displayText removeObjectForKey:General/[NSNumber numberWithInt:location]];
}
- (void)clearAttributedTitles {
	[displayText removeAllObjects];
}
-(General/NSDictionary *)getAttributedTitles {
	return displayText;
}


-(void)drawTitlesWithFrame:(General/NSRect)frame inView:(General/NSView*)controlView {
	General/NSEnumerator *enumerator = [displayText keyEnumerator];
	id textPosition;
	id drawObject;
	General/NSSize contentBoundaries;
	while ((textPosition = [enumerator nextObject])) {
		int textPositionValue = [textPosition intValue];
		drawObject = [displayText objectForKey:textPosition];
		contentBoundaries = [drawObject size];
		switch (textPositionValue) {
			case General/TopLeft:
				if([drawObject isKindOfClass:General/[NSAttributedString class]]) {
					[(General/NSAttributedString*)drawObject drawAtPoint:General/NSMakePoint(0,(frame.size.height-contentBoundaries.height))];
				}
				break;
			case General/TopRight:
				if([drawObject isKindOfClass:General/[NSAttributedString class]]) {
					[(General/NSAttributedString*)drawObject drawAtPoint:General/NSMakePoint(frame.size.width-contentBoundaries.width,frame.size.height-contentBoundaries.height)];
				}
				break;
			case General/TopCenter:
				if([drawObject isKindOfClass:General/[NSAttributedString class]]) {
					[(General/NSAttributedString*)drawObject drawAtPoint:General/NSMakePoint((frame.size.width-contentBoundaries.width)/2,(frame.size.height-contentBoundaries.height))];
				}
				break;
			case General/LeftCenter:
				if([drawObject isKindOfClass:General/[NSAttributedString class]]) {
					[(General/NSAttributedString*)drawObject drawAtPoint:General/NSMakePoint(0,(frame.size.height-contentBoundaries.height)/2)];
				}
				break;
			case General/RightCenter:
				if([drawObject isKindOfClass:General/[NSAttributedString class]]) {
					[(General/NSAttributedString*)drawObject drawAtPoint:General/NSMakePoint((frame.size.width-contentBoundaries.width),(frame.size.height-contentBoundaries.height)/2)];
				}
				break;
			case Center:
				if([drawObject isKindOfClass:General/[NSAttributedString class]]) {
					[(General/NSAttributedString*)drawObject drawAtPoint:General/NSMakePoint((frame.size.width-contentBoundaries.width)/2,(frame.size.height-contentBoundaries.height)/2)];
				}
				break;
			case General/BottomLeft:
				if([drawObject isKindOfClass:General/[NSAttributedString class]]) {
					[(General/NSAttributedString*)drawObject drawAtPoint:General/NSMakePoint(0,0)];
				}
				break;
			case General/BottomRight:
				if([drawObject isKindOfClass:General/[NSAttributedString class]]) {
					[(General/NSAttributedString*)drawObject drawAtPoint:General/NSMakePoint((frame.size.width-contentBoundaries.width),0)];
				}
				break;
			case General/BottomCenter:
				if([drawObject isKindOfClass:General/[NSAttributedString class]]) {
					[(General/NSAttributedString*)drawObject drawAtPoint:General/NSMakePoint((frame.size.width-contentBoundaries.width)/2,0)];
				}
				break;
			default:
				break;
		}
	}
}


// Overrideing this method prevents annoying 'Button' text
- (General/NSRect)drawTitle:(General/NSAttributedString*)title withFrame:(General/NSRect)cellFrame inView:(General/NSView*)controlView {
	return cellFrame; 
}

- (void)drawBezelWithFrame:(General/NSRect)cellFrame inView:(General/NSView *)controlView {
	[self drawTitlesWithFrame:cellFrame inView:controlView];
}





Hope it helps out other people

QUICK NOTE: You might want to put in an offset on the text close the either side of the button as its smushed right up against the side on some buttons as the actual button image does not always fill the entire button frame (especially with rounded or round buttons). Just play with it to see what I mean.
