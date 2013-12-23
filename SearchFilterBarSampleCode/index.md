---
layout: page
title: SearchFilterBarSampleCode
---

This is the depository for three classes which together form the base code for my implementation of the search filter bar seen in itunes, safari, finder, and mail.

The hope is that if you want to do it your own way, here's all the code.  But I plan on creating an IB palette for the whole project once it's polished off.  So just check back in.

Note that the code references 17 outside images, 15 of which can be found in Mail.app's bundle and are the basis for the button background in that style.  The other two are tiling strips for the two background styles (itunes and mail styles), and I guess if you want them you should post here telling me where I can drop them online and have them accessible.  No way I'm putting them in my .mac account.

Current status:
correct display of all 3 styles with corresponding buttons;
does NOT connect to a delegate, therefore there is only one button in the bar (but it's a purdy button!)

Heads up:
the three classes are:
FilterBar
SDFilterBarButton
SDFilterBarButtonCell

----

    
/* FilterBar */

#import <Cocoa/Cocoa.h>
#import "SDFilterBarButton.h"
#import "SDFilterBarButtonCell.h"


@interface FilterBar : NSView
{
	NSImage *backgroundImage;
	NSMutableArray *buttons;
}

- (NSButton *)newButton;
- (BOOL)addButtonWithTitle:(NSString *) representedObject:(id)repObject toFamily:(NSString *)family;

- (id)delegate;
- (void)setDelegate:(id)delegate;

@end


#import "FilterBar.h"

@implementation FilterBar

#pragma mark -
#pragma mark Setup

- (id)initWithFrame:(NSRect)frameRect
{
	if ( (self = [super initWithFrame:frameRect]) == nil )
		return nil;
	
	backgroundImage = [[NSImage imageNamed:@"FilterBarBackground"] retain];
	buttons = [[NSMutableArray alloc] init];
	
	[SDFilterBarButton setCellClass:[SDFilterBarButtonCell class]];
	
	return self;
}

- (void)awakeFromNib
{
	//self window] setAcceptsMouseMovedEvents:YES];
	
	[[SDFilterBarButton *newButton;
	newButton = [self newButton];

	NSRect frame = [newButton frame];
	frame.origin.x = 4;
	frame.origin.y = 2;
	[newButton setFrame:frame];
	
	newButton cell] setBezeled:NO];
	
	[buttons addObject:newButton];
	
	[self addSubview:newButton];
}

#pragma mark -
#pragma mark Display

- (void)drawRect:([[NSRect)rect
{
	NSLog(@"Displaying");
	[super drawRect:rect];
	[backgroundImage compositeToPoint:NSMakePoint(0,0) operation:NSCompositeCopy]; //NSCompositeSourceOver
}

#pragma mark -
#pragma mark Cell creation

- (NSButton *)newButton
{
	SDFilterBarButton *newButton = [[SDFilterBarButton alloc] init];
	[newButton setTitle:@"button title"];
	[newButton setButtonType:NSPushOnPushOffButton];
	[newButton setBezelStyle:NSRecessedBezelStyle];
	[newButton sizeToFit];
	//newButton cell] setBackgroundColor:nil];
	
	[[newButton cell] setHighlighted:NO];
	[[newButton cell] setShowsBorderOnlyWhileMouseInside:NO]; //NO
	return newButton;
}

- (BOOL)addButtonWithTitle:([[NSString *) representedObject:(id)repObject
{
	
}

#pragma mark -
#pragma mark Expiremental

- (void)mouseMoved:(NSEvent *)theEvent
{
	NSLog(@"YAY!");
}

- (void)mouseEntered:(NSEvent *)theEvent
{
	NSLog(@"mouse in filterBar");
}

#pragma mark -
#pragma mark Tracking rects

- (void)addSubview:(NSView *)aView
{
	NSLog(@"addSubview");
	[self setValue:[self addTrackingRect:[aView frame] 
								   owner:aView 
								userData:nil 
							assumeInside:NO] 
		forKeyPath:[NSString stringWithFormat:@"subviews.%@.tag", [aView title]]];
	
	[super addSubview:aView];
}

- (void)willRemoveSubview:(NSView *)subview
{
	NSLog(@"willRemoveSubview");
	[self removeTrackingRect:self valueForKeyPath:[[[NSString stringWithFormat:@"subviews.%@.tag", [subview title]]] intValue]];
	
	[super willRemoveSubview:subview];
}


@end



    

#import <Cocoa/Cocoa.h>
#import "SDFilterBarButtonCell.h"


@interface SDFilterBarButton : NSButton 
{

}

@end




#import "SDFilterBarButton.h"


@implementation SDFilterBarButton

#pragma mark -
#pragma mark Setup

- (id)init
{
	if ( ![super init] )
		return nil;
	
	[self setTarget:self];
	[self setAction:@selector(buttonDown:)];	
	
	return self;
}

#pragma mark -
#pragma mark Mouse tracking

- (void)mouseEntered:(NSEvent *)event
{
	self cell] setValue:[[[NSNumber numberWithBool:YES] forKey:@"mouseInView"];
	[self setNeedsDisplay:YES];
}

- (void)mouseExited:(NSEvent *)event
{
	self cell] setValue:[[[NSNumber numberWithBool:NO] forKey:@"mouseInView"];
	[self setNeedsDisplay:YES];
}

#pragma mark -
#pragma mark Action

- (IBAction)buttonDown:(id)sender
{
	self cell] toggleSelected];
}

@end



    


#import <Cocoa/Cocoa.h>


@interface [[SDFilterBarButtonCell : NSButtonCell
{
	BOOL selected;
	BOOL mouseInView;
}

- (void)toggleSelected;

@end


#import "SDFilterBarButtonCell.h"


@implementation SDFilterBarButtonCell

#pragma mark -
#pragma mark Setup

- (id)init
{
	if ( ![super init] )
		return nil;
		
	selected = NO;
	mouseInView = NO;
	
	return self;
}

#pragma mark -
#pragma mark Selection logic

- (void)toggleSelected
{
	if ( selected == YES ) {
		selected = NO;	
	} else {
		selected = YES;	
	}
}

#pragma mark -
#pragma mark Drawing

- (void)drawBezelWithFrame:(NSRect)frame inView:(NSView*)controlView
{
	if ( !mouseInView && !selected )
		return;	
		
	[super drawBezelWithFrame:frame inView:controlView];
}

- (NSRect)drawTitle:(NSAttributedString*)title withFrame:(NSRect)frame inView:(NSView*)controlView
{
	if ( mouseInView || selected )
		return [super drawTitle:title withFrame:frame inView:controlView];
		
	NSMutableAttributedString *prettyTitle = [[NSMutableAttributedString alloc] initWithAttributedString:title];
	[prettyTitle addAttribute:NSForegroundColorAttributeName value:[NSColor blackColor] range:NSMakeRange(0, [title length])]; //darkGrayColor
	
	NSShadow *shadow = [[NSShadow alloc] init];
		[shadow setShadowColor:[NSColor whiteColor]];
		[shadow setShadowBlurRadius:2];
		[shadow setShadowOffset:NSMakeSize(1, -1)];
	[prettyTitle addAttribute:NSShadowAttributeName value:shadow range:NSMakeRange(0, [title length])];
	
	return [super drawTitle:prettyTitle withFrame:frame inView:controlView];
}

@end


