---
layout: page
title: MailStyleNSSplitview
---

<span style="color:red">**This is irrelevant in 10.5+ and not worth a topic since it's a simply an option in IB.**</span>

----

Note that in Leopard, this effect can be achieved by setting the NSSplitView to use a thin divider (in Interface Builder) and the additional grabber can be made by implementing the 

    - (NSRect)splitView:(NSSplitView *)splitView additionalEffectiveRectOfDividerAtIndex:(NSInteger)dividerIndex


delegate method. -BB

(More information on this in NSSplitViewWithResizeControl)

----


I am trying to design a splitview like it mail, the one that seperates the mail boxs from the message. I've managed to get the one pixel splitter to look right, but i haven't had any luck when it comes to the grabber. Any suggestions?

----

Take a look at ResizeCustomViewOnDrag to see how to get the drag and drop to work right. You need this in a custom view that draws the grip however you wish and defines a "grip rect", a rectangle defining your grip's area. In the example, "GRIPRECT" could be a macro or even just a function like [self gripRect] that calculates and returns the grip rectangle. The mouseDown method in the example will check to see if the grip rect is being dragged and reacts accordingly.

----

RBSplitView can do this also. You can get the Mail grab image/look from RSControls [http://blogs.roguesheep.com/2006/10/23/for-your-ui-pleasurerscontrols]

----
Here is my solution, which I basically stole from Colloquy but slimmed down and added the autosave function listed on another page here.  It relies on three classes: a subclass of NSSplitview, a view class for the background, and a view class for the slider portion.  You also will need to rob the slider image from Mail.app and dissect a 1-pixel-wide shot of the part that expands.  Basically, you configure it like so:

* Add the three classes below to your project
* Add the two necessary images to your project, and just make sure that the names of the images correspond to what's called in the files.  In the example, they are called "MailSliderBackground" and "sidebarResizeWidget"
* In an empty window, place an NSView object and set it to resize horizontally, and make it of class MailStyleFunctionBarBackground.  It should be 23 pixels high and around 100 wide.
* Drag an NSImageView to the right side of this view, and make it of class SDSliderImageView.  Set the Image: as "sidebarResizeWidget" right there in IB
* Place other buttons you might want to the left, and maybe an NSOutlineview above it, set to resize but to keep its distance from the bottom.
* Select all 3 or more items you have created and group them under a custom view via the "Layout -> make subviews of" menu
* Select this new view and your right-side view and make make them subviews of a splitview.  Set up your splitview and make it of class MySplitView.  The left side should contain your view
* You should be good. Your splitView should now look like this in IB: 


http://idisk.mac.com/omnius/Public/MailSplitView.tiff

MySplitView
    
#import <Cocoa/Cocoa.h>

@interface MySplitView : NSSplitView
{
	IBOutlet id leftView;
	IBOutlet id resizeSlider;

	IBOutlet id outlineView;  //You have to make this in your IB
	IBOutlet id actionButon;  //You have to make this in your IB
	IBOutlet id favoritesButton; //You have to make this in your IB
	
	BOOL inResizeMode;
}

- (IBAction)adjustSubviews:(id)sender;

- (void)storeLayoutWithName:(NSString *)name;
- (void)loadLayoutWithName:(NSString *)name;

@end

#import "MySplitView.h"

@implementation MySplitView

#pragma mark -
#pragma mark Original Methods

- (void)awakeFromNib
{
	[self setDelegate:self];
}

- (IBAction)adjustSubviews:(id)sender
{
	[self adjustSubviews];
}

#pragma mark -
#pragma mark Overridden from NSSplitView

- (float)dividerThickness
{
	return .5;
}

- (void)drawDividerInRect:(NSRect)aRect
{
	[[NSColor colorWithDeviceWhite:.75 alpha:1] setFill];
	[NSBezierPath fillRect:aRect];
}

#pragma mark -
#pragma mark NSSplitView delegate methods

- (BOOL)splitView:(NSSplitView *)sender canCollapseSubview:(NSView *)subview
{
	if ( subview className] isEqualToString:@"[[NSScrollView"] ) //What's in the right view
		return NO;
		
	return YES;
}

- (float)splitView:(NSSplitView *)sender constrainMaxCoordinate:(float)proposedMax ofSubviewAt:(int)offset
{
	return 260.0; //Max width
}

- (float)splitView:(NSSplitView *)sender constrainMinCoordinate:(float)proposedMin ofSubviewAt:(int)offset
{
	return 140.0; //Min width
}

- (void)splitView:(NSSplitView *)sender resizeSubviewsWithOldSize:(NSSize)oldSize
{	
	NSRect newFrame = [sender frame]; // get the new size of the whole splitView
	NSView *left = sender subviews] objectAtIndex:0];
		[[NSRect leftFrame = [left frame];
	NSView *right = sender subviews] objectAtIndex:1];
		[[NSRect rightFrame = [right frame];
 
	float dividerThickness = [sender dividerThickness];
      
	leftFrame.size.height = newFrame.size.height;

	rightFrame.size.width = newFrame.size.width - leftFrame.size.width - dividerThickness;
	rightFrame.size.height = newFrame.size.height;
	rightFrame.origin.x = leftFrame.size.width + dividerThickness;

	[left setFrame:leftFrame];
	[right setFrame:rightFrame];
}

#pragma mark -
#pragma mark Sidebar resize area

- (void)resetCursorRects
{
	[super resetCursorRects];
		
	NSRect location = [resizeSlider frame];
		location.origin.y = [self frame].size.height - location.size.height;

	[self addCursorRect:location cursor:[NSCursor resizeLeftRightCursor]];
}

- (void)mouseDown:(NSEvent *)theEvent 
{
	//NSLog(@"mouseDown in splitView");
	NSPoint clickLocation = [theEvent locationInWindow];

	NSView *clickReceiver = [self hitTest:clickLocation];
	if ( clickReceiver className] isEqualToString:@"[[SDSliderImageView"] ) {
		//NSLog(@"Entering drag");
		inResizeMode = YES;
	} else {
		//NSLog([clickReceiver className]);
		inResizeMode = NO;
		[super mouseDown:theEvent];
	}
	//NSLog(@"mouseDown in splitView done");
}

- (void)mouseUp:(NSEvent *)theEvent
{
	//NSLog(@"Exiting drag");
	inResizeMode = NO;
}

- (void)mouseDragged:(NSEvent *)theEvent 
{
	//NSLog(@"mouseDragged in splitView");
	if ( inResizeMode == NO ) {
		[super mouseDragged:theEvent];
		return;
	}
		
	[[NSNotificationCenter defaultCenter] postNotificationName:NSSplitViewWillResizeSubviewsNotification object:self];
	
    NSPoint clickLocation = [theEvent locationInWindow];	
	NSRect newFrame = [leftView frame];
		newFrame.size.width = clickLocation.x;
	
	id delegate = [self delegate];
	if( delegate && [delegate respondsToSelector:@selector( splitView:constrainSplitPosition:ofSubviewAt: )] ) {
		float new = [delegate splitView:self constrainSplitPosition:newFrame.size.width ofSubviewAt:0];
		newFrame.size.width = new;
		//NSLog(@"Constrained width to: %f", new);
	}
	
	if( delegate && [delegate respondsToSelector:@selector( splitView:constrainMinCoordinate:ofSubviewAt: )] ) {
		float min = [delegate splitView:self constrainMinCoordinate:0. ofSubviewAt:0];
		newFrame.size.width = MAX( min, newFrame.size.width );
		//NSLog(@"Constrained width to: %f", newFrame.size.width);
	}
	
	if( delegate && [delegate respondsToSelector:@selector( splitView:constrainMaxCoordinate:ofSubviewAt: )] ) {
		float max = [delegate splitView:self constrainMaxCoordinate:0. ofSubviewAt:0];
		newFrame.size.width = MIN( max, newFrame.size.width );
		//NSLog(@"Constrained width to: %f", newFrame.size.width);
	}
	
	[leftView setFrame:newFrame];
	[self adjustSubviews];
	
	[[NSNotificationCenter defaultCenter] postNotificationName:NSSplitViewDidResizeSubviewsNotification object:self];
}


#pragma mark -
#pragma mark Position save support

- (NSString*)ccd__keyForLayoutName: (NSString*)name
{
	return [NSString stringWithFormat: @"CCDNSSplitView Layout %@", name];
}

- (void)storeLayoutWithName: (NSString*)name
{
	NSString*		key = [self ccd__keyForLayoutName: name];
	NSMutableArray*	viewRects = [NSMutableArray array];
	NSEnumerator*	viewEnum = self subviews] objectEnumerator];
	[[NSView*			view;
	NSRect			frame;
	
	while( (view = [viewEnum nextObject]) != nil )
	{
		if( [self isSubviewCollapsed: view] )
			frame = NSZeroRect;
		else
			frame = [view frame];
		
		[viewRects addObject: NSStringFromRect( frame )];
	}
	
	[[NSUserDefaults standardUserDefaults] setObject: viewRects forKey: key];
}

- (void)loadLayoutWithName: (NSString*)name
{
	NSString*		key = [self ccd__keyForLayoutName: name];
	NSMutableArray*	viewRects = [[NSUserDefaults standardUserDefaults] objectForKey: key];
	NSArray*		views = [self subviews];
	int				i, count;
	NSRect			frame;
	
	count = MIN( [viewRects count], [views count] );
	
	for( i = 0; i < count; i++ )
	{
		frame = NSRectFromString( [viewRects objectAtIndex: i] );
		if( NSIsEmptyRect( frame ) )
		{
			frame = views objectAtIndex: i] frame];
			if( [self isVertical] )
				frame.size.width = 0;
			else
				frame.size.height = 0;
		}
		
		[[views objectAtIndex: i] setFrame: frame];
	}
}

@end



[[MailStyleFunctionBarBackground
    
#import <Cocoa/Cocoa.h>

@interface MailStyleFunctioBarBackgroundView : NSView
{
	NSImage *backgroundImageForSelectedStyle;
}

@end

#import "MailStyleFunctioBarBackgroundView.h"

@implementation MailStyleFunctioBarBackgroundView

- (id)initWithFrame:(NSRect)frameRect
{
	if ((self = [super initWithFrame:frameRect]) != nil) {
		backgroundImageForSelectedStyle = [NSImage imageNamed:@"MailSliderBackground"];
	}
	return self;
}

#pragma mark -
#pragma mark Display

- (void)drawRect:(NSRect)rect
{
	//NSLog(@"drawRect");
	//Draw the background, tiling a 1px-wide image horizontally	
	[backgroundImageForSelectedStyle drawInRect:[self bounds]
									   fromRect:NSMakeRect(0, 0, [backgroundImageForSelectedStyle size].width, [backgroundImageForSelectedStyle size].height)
									  operation:NSCompositeSourceAtop 
									   fraction:1.0];
	[super drawRect:rect];
}

@end


SDSliderImageView
    
#import <Cocoa/Cocoa.h>

@interface SDSliderImageView : NSImageView
{
}
@end

#import "SDSliderImageView.h"

@implementation SDSliderImageView

- (void)mouseDown:(NSEvent *)theEvent
{
	//NSLog(@"mouseDown in sliderImage");
	[self superview] superview] mouseDown:theEvent];
}

- (void)mouseDragged:([[NSEvent *)theEvent
{
	//NSLog(@"mouseDragged in sliderImage");
	[self superview] superview] mouseDragged:theEvent];
}

@end


----

I've posted a sample project that demonstrates some good techniques for creating this type of layout:

[http://www.mere-mortal-software.com/blog/details.php?d=2006-12-21]

http://www.mere-mortal-software.com/blog/blogimages/2006/12/21/[[FinalWindow.jpg

----

Any chance to see this palette compatible with the new Interface Builder 3?

----

What the heck doesn't somebody make an open source cocoa framework with all of this stuff in it. I've still very new to Cocoa and all this stuff is what I want in my app, but I really shouldn't have to keep copy-n-pasting this stuff all of the place. 

----

If it's an issue for you, it's open-source: release your own framework. We don't get paid to put this code out and we don't charge you anything for it. You get what you pay for.

