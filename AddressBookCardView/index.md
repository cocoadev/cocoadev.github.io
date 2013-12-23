---
layout: page
title: AddressBookCardView
---

I've often wondered and I expect others do as well. What is the "CardView" in Apple's Address book?

My guess is thats its an NSTextView (from the selection behavior) with assorted NSTextAttachmentCells around to implement the various fields. Perhaps an NSLayoutManager subclass to do the positioning? IIRC there was some chatter on cocoa-dev many moons ago about a WWDC session on the subject---it seems like it would be a good subject for sample code showing off NSTextView's flexibility if it is in fact an NSTextView.

----

Install FScriptAnywhere and you can check it out for yourself.

----

Now that is a nifty tool. Here's what I've learned so far:

The main view is, in fact, a subclass of NSTextView called ABTextView.

Most of the things you see are attributed strings rather than NSTextAttachmentCells. For instance, from the "Apple Computer" card we have:

    
Apple Computer Inc.{
    ABFieldsAttribute = ABFieldsAttribute; 
    ABTextViewAttribute = Organization; 
    ABTitleAttribute = ABTitleAttribute; 
    NSColor = NSCalibratedWhiteColorSpace 0 1; 
    NSFont = "CGS Helvetica-Bold 16.00 pt. P [] (0x00373d40) fobj=0x003ea150, spc=4.45"; 
}


It appears they're using something similar to the code used in LayoutManagerDemo to handle the rollover decoration and (presumably) the popup menus. There also appear to be a form of field divider (maybe to speed up layout):

    
{
    ABDividerAttribute = ABDividerAttribute; 
    ABFieldsAttribute = ABFieldsAttribute; 
    ABTitleAttribute = ABTitleAttribute; 
    NSFont = "CGS Helvetica-Bold 16.00 pt. P [] (0x00373d40) fobj=0x003ea150, spc=4.45"; 
}


Most interestingly is that the NSImageView subclass (ABImageView) that holds the vCard picture does not appear to be an image attachment. Rather, it looks like there's an NSTextContainer subclass that takes a notch out of one corner. For example, if we wanted to take an 80x80 pixel notch (it happens to be the size of the image I'm using for testing):

    
@implementation NotchedTextContainer
- (BOOL)isSimpleRectangularTextContainer { return NO; }
- (BOOL)containsPoint:(NSPoint)aPoint {
	if(aPoint.x <= 80.0 && aPoint.y <= 80.0)
		return NO;
	return YES;
}
- (NSRect)lineFragmentRectForProposedRect:(NSRect)proposedRect 
						   sweepDirection:(NSLineSweepDirection)sweepDirection 
						movementDirection:(NSLineMovementDirection)movementDirection 
							remainingRect:(NSRect *)remainingRect {
	if(NSMinY(proposedRect) < 80.0)
		proposedRect.origin.x = 80.0;
	return [super lineFragmentRectForProposedRect:proposedRect
								sweepDirection:sweepDirection
							 movementDirection:movementDirection
								 remainingRect:remainingRect];
}
@end


To simulate the separator line above the "Note:" I've worked up this small example cell that is simply added by means of the usual NSTextAttachment:

    
@implementation SeparatorCell
- (NSRect)cellFrameForTextContainer:(NSTextContainer *)textContainer 
			   proposedLineFragment:(NSRect)lineFrag 
					  glyphPosition:(NSPoint)position 
					 characterIndex:(unsigned)charIndex {
	NSRect frag = [super cellFrameForTextContainer:textContainer
							  proposedLineFragment:lineFrag
									 glyphPosition:position
									characterIndex:charIndex];
	NSSize size = [textContainer containerSize];
	size.width -= 2.0*[textContainer lineFragmentPadding];
	
	frag.size.width = size.width - frag.origin.x;
	return frag;
}
- (void)drawWithFrame:(NSRect)frame
			   inView:(NSView*)aView {
	NSGraphicsContext *ctx = [NSGraphicsContext currentContext];
	[NSBezierPath strokeLineFromPoint:NSMakePoint(frame.origin.x,frame.origin.y+(frame.size.height/2))
							  toPoint:NSMakePoint(frame.origin.x+frame.size.width,frame.origin.y+(frame.size.height/2))];
}
@end


Obviously, there is a lot more that could be done to customize line thickness and whatnot (perhaps make it sensitive to the line height...), but this question has popped up on cocoa-dev before, so I figured I'd put some google-visible code up.

