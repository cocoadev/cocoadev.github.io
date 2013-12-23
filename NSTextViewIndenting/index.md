---
layout: page
title: NSTextViewIndenting
---

I submitted this question to cocoadev mailing list with no luck.  I'm having trouble indenting my text with a custom nstextview.  I want it indented in relation to the top and bottom, leaving a space of about 1 pixel above the text, and below.  To do this, currently I am using [textView setTextContainerInset: NSMakeSize(...)];  This works well enough, but break my border drawing code, which is the following:

    
@implementation NSTextViewSubclass

- (void)drawRect:(NSRect)clipRect
{

	[super drawRect];

	[NSBezierPath strokeLineFromPoint: NSMakePoint(NSMinX(bounds)+0.5,NSMinY(bounds)) toPoint: NSMakePoint(NSMinX(bounds)+0.5,NSMaxY(bounds))];
	[NSBezierPath strokeLineFromPoint: NSMakePoint(NSMaxX(bounds)-0.5,0.0) toPoint: NSMakePoint(NSMaxX(bounds)-0.5,NSMaxY(bounds))];
	[NSBezierPath strokeLineFromPoint: NSMakePoint(NSMinX(bounds),NSMinY(bounds)+0.5) toPoint: NSMakePoint(NSMaxX(bounds),NSMinY(bounds)+0.5)];
	[NSBezierPath strokeLineFromPoint: NSMakePoint(NSMinX(bounds),NSMaxY(bounds)-0.5) toPoint: NSMakePoint(NSMaxX(bounds),NSMaxY(bounds)-0.5)];

}

@end


For some reason, when I set a container inset, the top of my border is lost, as if the container is now being drawn on TOP of the border.  Does anyone know why this is happening/a workaround/ a different method to create the inset?

Note: I cannot place the textview in a scroll view since it is being used as a field editor, so I can't use NSScrollView's border.

Thanks in advance,

- FranciscoTolmasky

