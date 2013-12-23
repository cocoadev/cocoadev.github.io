---
layout: page
title: CCDGrowingTextField
---

This is just a simple NSTextField that resizes to fit its contents.

    
#import <Cocoa/Cocoa.h>

@interface CCDGrowingTextField : NSTextField
{
	NSRect baseFrame;
}
- (NSRect)baseFrame;
- (void)setBaseFrame:(NSRect)frame;

- (NSSize)contentSize;
@end


    
#import "CCDGrowingTextField.h"

@implementation CCDGrowingTextField

- (id)initWithFrame:(NSRect)frame
{
	if (self = [super initWithFrame:frame]) baseFrame = frame;

	return self;
}
- (id)initWithCoder:(NSCoder*)coder {

   if (self = [super initWithCoder:coder]) baseFrame = [self frame];
   
   return self;
}

- (NSRect)baseFrame { return baseFrame; }

- (void)setBaseFrame:(NSRect)frame
{
	baseFrame = frame;
}

- (void)sizeToFit
{
	NSRect frame = [self frame];
		frame.size = [self contentSize];

	[self setFrame:frame];
}

- (void)textDidChange:(NSNotification *)aNotification
{
	if (self stringValue] isEqualToString:@""]) {
		[self setFrame:baseFrame];
		return;
	}
	[self sizeToFit];
}

- ([[NSSize)contentSize
{
	NSTextView *textView = (NSTextView*)self window] fieldEditor:YES forObject:self];

return [[textView layoutManager] usedRectForTextContainer:[textView textContainer.size;
}

@end


----
I noticed that this grows (what would be) vertically when it's rotated. In that case you might rework the     contentSize method as such..
    
- (NSSize)contentSize
{
NSSize approxSize = self attributedStringValue] size];
	approxSize.width += 10; // a bit more than enough

return approxSize;
}


----

Both bits of code above will expand the field by its width, but suppose that I want the field to be constrained to within the bounds of its superview, and wrap to the next line. I can use [self setWraps:YES], but how do I recalculate the field's frame to expand the proper height, and make sure to not get too wide?

----
You may want to take a look at [[IFVerticallyExpandingTextField.  This sounds more like what you're aiming for.  At the least, you can borrow some ideas from it for calculating the proper height.

----

Thanks for the sample code. Here's a slightly modified version that can handle text fields that resize their widths.  It also doesn't reset the width of an empty text field to default until the user stops editing. CCDGrowingTextFieldSample --SaileshAgrawal

