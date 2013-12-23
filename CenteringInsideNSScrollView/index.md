---
layout: page
title: CenteringInsideNSScrollView
---


----
**Overview**
----

If you want to center the document view inside of a scrollview when the document view is smaller than the scrollview, you have a couple options. You can:


* Rearchitect the document view so that it can draw its content centered within itself when needed
* Put the document view inside another custom view, where the latter centers the subview.
* Create a custom NSClipView which centeres the document view



The first option is simple, but can be annoying to deal with and cause other problems within the document view. The second option is the obvious evolution of the first splitting the responsibilities into two views. It's easy to implement, but there always seems to be some unexpect gotcha everytime I've done it. The third solution attacks the problem head on, making the clip view center the document view which makes the most sense because the clip view is responsible for the actual layout.


----
**Centering NSClipView**
----

There is an article (dated 2002, but still relevant) which shows one approach to creating a centering clip view. The article is available here: http://www.bergdesign.com/developer/index_files/88a764e343ce7190c4372d1425b3b6a3-0.html

In my experience, this code no longer works out of the box. I have tweaked the code, and it now works properly in all of my tests. 

The example project for this is here:
http://www.sethwillits.com/temp/CenteredScrollView.zip


The only drawback to using an NSClipView subclass is that you need to replace the clipview in the scrollview at runtime since it's not possible in Interface Builder / Xcode 4. The AGCenteringClipView class includes a convenience method to aid with this.


    
//
//  AGCenteringClipView.h
//  CenteredScroll
//
//  Created by Seth Willits on 8/15/2011.
//

#import <Cocoa/Cocoa.h>


@interface AGCenteringClipView : NSClipView {
	NSPoint mLookingAt; // the proportion up and across the view, not coordinates.
}

+ (void)replaceClipViewInScrollView:(NSScrollView *)scrollView;

@end



    
//
//  AGCenteringClipView.m
//  CenteredScroll
//
//  Created by Seth Willits on 8/15/2011.
//

#import "AGCenteringClipView.h"


@interface AGCenteringClipView (Private)
- (void)centerDocument;
@end



@implementation AGCenteringClipView

+ (void)replaceClipViewInScrollView:(NSScrollView *)scrollView
{
	NSView * docView = scrollView documentView] retain];
	[[AGCenteringClipView * newClipView = nil;
	
	newClipView = [self class] alloc] initWithFrame:[[scrollView contentView] frame;
	[newClipView setBackgroundColor:scrollView contentView] backgroundColor;
	
	[scrollView setContentView:(NSClipView *)newClipView];
	[scrollView setDocumentView:docView];
	
	[newClipView release];
	[docView release];
}


// ----------------------------------------
// We need to override this so that the superclass doesn't override our new origin point.
- (NSPoint)constrainScrollPoint:(NSPoint)proposedNewOrigin
{
	NSRect docRect = self documentView] frame];
	[[NSRect clipRect = [self bounds];
	CGFloat maxX = docRect.size.width - clipRect.size.width;
	CGFloat maxY = docRect.size.height - clipRect.size.height;

	clipRect.origin = proposedNewOrigin; // shift origin to proposed location

	// If the clip view is wider than the doc, we can't scroll horizontally
	if (docRect.size.width < clipRect.size.width) {
		clipRect.origin.x = round( maxX / 2.0 );
	} else {
		clipRect.origin.x = round( MAX(0,MIN(clipRect.origin.x,maxX)) );
	}
	
	// If the clip view is taller than the doc, we can't scroll vertically
	if (docRect.size.height < clipRect.size.height) {
		clipRect.origin.y = round( maxY / 2.0 );
	} else {
		clipRect.origin.y = round( MAX(0,MIN(clipRect.origin.y,maxY)) );
	}
	
	// Save center of view as proportions so we can later tell where the user was focused.
	mLookingAt.x = NSMidX(clipRect) / docRect.size.width;
	mLookingAt.y = NSMidY(clipRect) / docRect.size.height;
	
	// The docRect isn't necessarily at (0, 0) so when it isn't, this correctly creates the correct scroll point
	return NSMakePoint(docRect.origin.x + clipRect.origin.x, docRect.origin.y + clipRect.origin.y);
}



// ----------------------------------------
// These two methods get called whenever the NSClipView's subview changes.
// We save the old center of interest, call the superclass to let it do its work,
// then move the scroll point to try and put the old center of interest
// back in the center of the view if possible.

- (void)viewBoundsChanged:(NSNotification *)notification
{
	NSPoint savedPoint = mLookingAt;
	[super viewBoundsChanged:notification];
	mLookingAt = savedPoint;
	[self centerDocument];
}

- (void)viewFrameChanged:(NSNotification *)notification
{
	NSPoint savedPoint = mLookingAt;
	[super viewFrameChanged:notification];
	mLookingAt = savedPoint;
	[self centerDocument];
}


// ----------------------------------------
// We have some redundancy in the fact that setFrame: appears to call/send setFrameOrigin:
// and setFrameSize: to do its work, but we need to override these individual methods in case
// either one gets called independently. Because none of them explicitly cause a screen update,
// it's ok to do a little extra work behind the scenes because it wastes very little time.
// It's probably the result of a single UI action anyway so it's not like it's slowing
// down a huge iteration by being called thousands of times.

- (void)setFrameOrigin:(NSPoint)newOrigin
{
	if (!NSEqualPoints(self.frame.origin, newOrigin)) {
		[super setFrameOrigin:newOrigin];
		[self centerDocument];
	}
}

- (void)setFrameSize:(NSSize)newSize
{
	if (!NSEqualSizes(self.frame.size, newSize)) {
		[super setFrameSize:newSize];
		[self centerDocument];
	}
}

- (void)setFrameRotation:(CGFloat)angle
{
	[super setFrameRotation:angle];
	[self centerDocument];
}

@end




#pragma mark  

@implementation AGCenteringClipView (Private)

- (void)centerDocument
{
	NSRect docRect = self documentView] frame];
	[[NSRect clipRect = [self bounds];

	// The origin point should have integral values or drawing anomalies will occur.
	// We'll leave it to the constrainScrollPoint: method to do it for us.
	if (docRect.size.width < clipRect.size.width) {
		clipRect.origin.x = (docRect.size.width - clipRect.size.width) / 2.0;
	} else {
		clipRect.origin.x = mLookingAt.x * docRect.size.width - (clipRect.size.width / 2.0);
	}
	
	
	if (docRect.size.height < clipRect.size.height) {
		clipRect.origin.y = (docRect.size.height - clipRect.size.height) / 2.0;
	} else {
		clipRect.origin.y = mLookingAt.y * docRect.size.height - (clipRect.size.height / 2.0);
	}
	
	// Probably the best way to move the bounds origin.
	// Make sure that the scrollToPoint contains integer values
	// or the NSView will smear the drawing under certain circumstances.
	[self scrollToPoint:[self constrainScrollPoint:clipRect.origin]];
	self superview] reflectScrolledClipView:self];
}

@end






----


The Seashore Project (http://seashore.sourceforge.net/) has an [[NSClipView subclass that does just this. The source is free to download (GPL'd, I believe), so give it a look. Specifically, check out CenteringClipView.h/m. - MattBall






----
**Centering Enclosing View**
----

In this example, there's a 4-level view hierarchy: NSScrollView -> NSClipView -> ScrollDocumentView -> MyView.

The ScrollDocumentView always makes sure to resize itself so that it is at minimum the size of the scrollview's clip view, and always large enough to contain the real "document view", the "MyView". MyView's responsibility is to always center itself in its superview when its frame changes.

It's important that the MyView instance have no autoresizing for width/height resizing, and that all of its margins remain flexible.




    
@implementation ScrollDocumentView

- (void)setFrame:(NSRect)frame;
{
	// Maintain a minimum size which fits the subview
	if (self.subviews.count) {
		NSView * subview = [self.subviews objectAtIndex:0];
		frame.size.width  = MAX(subview.frame.size.width,  self.enclosingScrollView.contentSize.width);
		frame.size.height = MAX(subview.frame.size.height, self.enclosingScrollView.contentSize.height);
	}
	
	
	// Can end up being recursive which
	// is why this bit has to be last.
	// (code above must run first)
	[super setFrame:frame];
}



- (void)didAddSubview:(NSView *)subview
{
	[super didAddSubview:subview];
	[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(viewFrameDidChange:) name:NSViewFrameDidChangeNotification object:subview];
}


- (void)willRemoveSubview:(NSView *)subview
{
	[super willRemoveSubview:subview];
	[[NSNotificationCenter defaultCenter] removeObserver:self name:NSViewFrameDidChangeNotification object:subview];
}


- (void)viewFrameDidChange:(NSNotification *)notification
{
	// When the subview's frame changes, we need
	// make sure this view is big enough to fit it
	self.frame = self.frame;
}


@end




@implementation MyView

- (void)setFrame:(NSRect)frame;
{
	NSRect contentFrame = NSZeroRect;
	contentFrame.size = self enclosingScrollView] contentSize];
	frame.origin.x = round([[NSMidX(contentFrame) - NSWidth(frame) / 2.0);
	frame.origin.y = round(NSMidY(contentFrame) - NSHeight(frame) / 2.0);
	[super setFrame:frame];
}


- (void)drawRect:(NSRect)rect;
{
	[[NSColor whiteColor] set];
	NSRectFill([self bounds]);
	
	[[NSColor blackColor] set];
	NSFrameRect([self bounds]);
}

@end





The example project for this is here:
http://www.sethwillits.com/temp/CenteredScrollView.zip







----
**Miscellaneous**
----

Note: not mentioned in this article, is that it appears you need to also provide this method in the view you are drawing:

    
- (BOOL)copiesOnScroll
{
	return NO;
}


When I did not do this, I was getting double images in the view. I guess if you really wanted to get "smart" about this, you would set NO if the document view was getting centered, and YES if not. 



----


That's exactly what I did, and the code for copiesOnScroll looks like this
    
- (BOOL)copiesOnScroll
{
	NSRect docRect = self documentView] frame];
	[[NSRect clipRect = [self bounds];

	return (roundf(docRect.size.width - clipRect.size.width) >= 0) && (roundf(docRect.size.height - clipRect.size.height) >= 0);
}




----



I had the same problem as the original poster, wanting to center an image (within an NSImageView) inside an NSScrollView. I have also experimented with subclassing the NSClipView, but for the very problem of simply centering an image, I found another solution to be much easier and faster. I took advantage of NSImageView centering its image automatically if the image is smaller than the view's frame. Therefore I simply sublassed NSImageView (similarly as in the very elegant MarginView example above), having it resize to whatever is larger, its image or the frame of the containing NSClipView. It's that easy... Here's the code:

    
In the .h file:

#import <Cocoa/Cocoa.h>

@interface SBSScrollableImageView : NSImageView {

	BOOL			insideScrollView;
}


@end

In the .m file:

#import "SBSScrollableImageView.h"

@implementation SBSScrollableImageView

- (id)initWithFrame:(NSRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
        insideScrollView = NO;
    }
    return self;
}


- (void)viewDidMoveToSuperview {
	insideScrollView = (self superview] isKindOfClass:[[[NSClipView class]]);
}


- (void) setFrame:(NSRect) newFrame {
	if (insideScrollView) {
		NSSize superSize = ((NSClipView*)_superview).frame.size;
		NSSize imageSize = ((NSImage*)[self image]).size;
		
		newFrame.size.width = MAX(imageSize.width, superSize.width);
		newFrame.size.height = MAX(imageSize.height, superSize.height);
	}
	[super setFrame:newFrame];
}

@end


Hope it helps someone (probably not the original poster with one year's delay)...

--MarCocoa

