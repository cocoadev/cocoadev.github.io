---
layout: page
title: ScalingTextView
---

Hello,

Does anybody know how to scale a text view without confusing the the scroll view? By "scale", I mean making the text bigger or smaller *without* affecting the actual *font* size. ie. Those with poor eyes can make the text on screen bigger (eg. 150%) without affecting how it will print out.

No matter what I do to try to achieve this, my scroll view gets confused in the following manner: at 100%, everything is fine. However, as soon as I scale to a larger size, a scroller appears in the scroll bar indicating that there is something to scroll, even when there isn't - eg. even when there is only one line of text (or none, in fact). (Notably, this becomes apparent only when I resize the window.)

The way I see it, there are two ways of scaling a text view: by using -scaleUnitSquareToSize:, or by changing the text view's bounds size (which is how TextEdit's ScalingScrollView does it - however, because scaling in TextEdit is only allowed when there are multiple containers - "Wrap to Page" mode - it seems to avoid the problem I am having). I have tried both, and both cause the same scroll view oddness. My code is as follows:

    
- (void)rescaleTextView:(NSTextView *)textView toScalePercent:(float)scalePercent
{	
	float currentScaleOffset = 1.0/([textView bounds].size.width/[textView frame].size.width);
	float scaleFactor = (scalePercent/100.0)/currentScaleOffset;
	
	// One method:
	NSSize scaleSize = NSMakeSize(scaleFactor,scaleFactor);
	[textView scaleUnitSquareToSize:scaleSize];

	// But I've also tried this:
	// [textView setBoundsSize:NSMakeSize([textView bounds].size.width/scaleFactor,
	//	[textView bounds].size.height/scaleFactor)];
	
	// We need to notifiy the clip view that the text view has changed size:
	[[NSNotificationCenter defaultCenter] postNotificationName:
		NSViewFrameDidChangeNotification
		object:textView];
	[textView setNeedsDisplayInRect:[textView visibleRect]];
}


(Note: I thought that the min size of the text container or some such might be causing the problem, but even where there is a lot of text so that a scroll view is actually needed, it can often happen that the scroller allows you to scroll too far - some way past the end of the text.)

Admittedly, this is only a minor annoyance (though it is an annoyance), and it does seem that I'm not the only one who has run into it. DEVONthink and Avenir (excellent shareware programs both) suffer from exactly the same "confused scroll view" problem when text is scaled, as can be seen from the shots on this page:

http://www.rumramruf.com/ScrivenerBeta/scaledScrollViewProblems.html

Has anybody else experienced this, or, even better, found a solution?

Many thanks in advance!
KB

----
If you can, I'd suggest forgetting about real Cocoa-drawing-like scaling, and just change the point size of the text within. You should be able to arrange things so you can set it back the way it was before, and this way everything is simple and known-working.

If you really want to handle scaling by fiddling with the bounds and such, then I'd suggest putting the NSTextView inside another view, and have that view be the documentView of the NSScrollView. Have your intermediary view resize itself appropriately to fit the NSTextView, and this way the NSScrollView can't become confused from the scaling, as it'll never look at subviews of the documentView.

----

Or you could scale the scroll view itself. Put it inside another view, and modify that view's frame and bounds to accomplish the scaling.

----
This will have the unfortunate side effect of also scaling the scroll bars, however.

