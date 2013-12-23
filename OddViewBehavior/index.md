---
layout: page
title: OddViewBehavior
---

**This is a menagerie that records some of the strange sights that people have seen over the years...**

Note that it contains embedded locators that may become invalid sometime in the future (but were valid as of Feb 02 2006) http://goo.gl/OeSCu

----

I was working on a subclass of NSView that draws a simple menu and I found an odd bug in it. I worked on it for a long time, but it doesn't look like the bug is in my source code. The subclass is supposed to draw a menu which is just a list of NSStrings. I created two slightly different versions of the view, one in which the bug appears and one in which it doesn't.

Bugged Example:
I wanted the menu to occupy the whole view so I took the view's height and divided it by the number of items in the menu to get each row's height. Before drawing any items, I set the current color to black (the background color). Then I had a loop which filled the current row's rect with the current color and then drew the string in that rect. Only the first row drawn (the bottom one) displayed with a black background.

Look at the screen shot:
http://www.pursuitgames.com/screen1.jpg

It appears that each row's background color is the color of the previous string's color. The strings' colors are supposed to alternate between white and green, but the background of all of the rows are supposed to be black.

Non-bugged Example:
This is almost the same as the bugged example except I changed the current rectangle's height. The current string is stored in the NSString, title, the attributes for my strings are stored in the NSMutableDictionary, attributes, and the current row's rect is stored in drawRect. The only change in the code between the two examples is in the following code. This line is added immediately before the rect is filled:

    drawRect.size.height = [title sizeWithAttributes:attributes].height;

Look at the screen shot:
http://www.pursuitgames.com/screen2.jpg

I am really confused about what is going on here. If anyone wants to try to help me understand the problem, you can download the project from http://www.pursuitgames.com/oddview.zip.

----

I don't really know what is going on with your code, but you can fix it by moving NSRectFill() to just below [bgColor set];. Also pass it rect instead of drawRect.  It should look like this when you are done:

    
[bgColor set];
NSRectFill(rect);    // pass it rect instead
for (i = [menuStrings count] - 1; i >= 0; i--)
{
	title = [menuStrings objectAtIndex:i];
	currentColor = (currentColor == colorOne) ? colorTwo : colorOne;
		
	[attributes setObject:currentColor forKey:NSForegroundColorAttributeName];
		
	if (mode == 1)
	{
		drawRect.size.height = [title sizeWithAttributes:attributes].height;
		NSLog(@"drawRect.size.height = %f", drawRect.size.height);
	}
		
	// moved from here
	[title drawInRect:drawRect withAttributes:attributes];
		
	drawRect.origin.y += drawRect.size.height;
}


----

Never mind, I do know what is going on, drawInRect:withAttributes: is setting the color and the next time you use NSRectFill it draws with the last color used by drawInRect:withAttributes:.  You can also fix your code by doing the following:

    
[bgColor set];
NSRectFill(drawRect);
[title drawInRect:drawRect withAttributes:attributes];


However, I recommend the first fix because it does less drawing.

----

What's odd is that by changing the row's height to the minimum rect's height that can contain the string, the bug goes away. I guess drawInRect:withAttributes: must use different code depending on if the specified rect's height is greater than the minimum rect's height.

I know that the first solution you gave me is more efficient, but I need to only fill each individual rect because all of the rects might not need to be redrawn. I'm using similar code to this in an optimized view. Is there a better fix than calling [bgColor set]; directly before the call to NSRectFill(drawRect);? Here's another solution I thought of, but I'm not sure which one is better:

    [bgColor set];
for (i = [menuStrings count] - 1; i >= 0; i--)
{
	NSRectFill(drawRect);
	drawRect.origin.y += drawRect.size.height;
}


drawRect.origin = NSZeroPoint;
drawRect.size = rowSize;
for (i = [menuStrings count] - 1; i >= 0; i--)
{
	title = [menuStrings objectAtIndex:i];
	currentColor = (currentColor == colorOne) ? colorTwo : colorOne;
		
	[attributes setObject:currentColor forKey:NSForegroundColorAttributeName];
		
	if (mode == 1)
	{
		drawRect.size.height = [title sizeWithAttributes:attributes].height;
		NSLog(@"drawRect.size.height = %f", drawRect.size.height);
	}
		
	// Call to NSRectFill(drawRect) is gone
	[title drawInRect:drawRect withAttributes:attributes];
		
	drawRect.origin.y += drawRect.size.height;
}


The differences are that [bgColor set]; will only be called once, but there will be two loops instead of just one. Which one do you think would be better to use?

----

Don't worry about how many times you change colors. This action is has the following profile:

*-[NSCalibratedRGBColor set] (a.k.a. -[NSColor set]) is a third order time issue in relation to -[NSBezierPath strokeRect] 
*-[NSCalibratedRGBColor set] is a second order time issue in relation to -[NSString(NSStringDrawing) drawInRect:withAttributes].


As a general rule, you should profile before optimizing your code (play with Sampler -> /Developer/Applications/Performance Tools/Sampler) --zootbobbalu

----

I tried out Sampler with my old and new code. I did two samples for each and both times, my new code's drawRect: method took up less time of the total amount of time the program ran than my old code. The tests were done on the menu I wrote and I tried to do the same things during all of the tests. I'm not sure how to tell if my new code is actually an improvement though because all of the calculations were done in a different method that is called when ever the frame changes instead of in the drawRect: method. Is there a way to change what is being displayed in a saved trace (samples, seconds, percent)?

I heard somewhere that Shark is much better than Sampler. Is that true and what other CHUD tools can I use to improve my drawing code?

*Look at it with QuartzDebug - don't think that's a CHUD tool though.*

----

**The following is a currently unsolved mystery that could not find a better topical home than this one to preserve it for posterity**

Moved from the erstwhile NS-View-Troubles to this slightly-less-ineptly-named topic, it is a true classic in the anals of OddViewBehavior, for you to study at your leisure:

----

I'm working on an application that has a custom window that is drawn with a view, with another view inside it, that is drawn based on user interaction. All is well until I set it as first responder, and have it redraw. For some reason, instead of drawing a line from one point to another, it takes the whole view, including the view drawn for the window, and shifts it down to the origin of the window's view. Basically what I see is the upper right hand corner of the window drawn in my view. Does anyone have any idea what is causing this? I'm sure it must be something simple that I'm overlooking. Any help would be greatly appreciated.

++Shad
----
When you say that one view is inside another view, does that mean that the second view is a subview, or are they overlapping views?
----
Sorry, the second view is a subview, but even if I make it in an overlapping view, it still does the same thing.

++Shad

----

My brain is still having problems parsing the problem.  Do you have some code or a screen shot we can look at?

----

A wild guess (I'm not really sure I understand the problem either) - It sounds like you're doing something with the view's     frame when you should be using     bounds

----

Before:
http://www.nancesoftware.com/shad/screen_1.gif

After:
http://www.nancesoftware.com/shad/screen_2.gif

++Shad

----

Whoa.  That's pretty wild.  Almost like the backing store is using the wrong stuff, or picking up things from the screen.  Is the view in question anything special, like an OpenGL view or something quicktime related?

----
Plain old NSView, drawn using NSBeizerPath. (Looking back, I should have been a bit more descriptive in my original post.)

++Shad

----

Maybe post some of the code?  Particularly the drawRect and the mouse tracking stuff.

----
There is no mouse tracking code. I simply have a view, which I now simply have overlaping the original, and am sending code to draw a black line halfway across the view. It only messes up after I set it as first responder, and have the view redrawn.

++Shad

----

Then post where you set it as first responder

----
    
- (BOOL)acceptsFirstResponder {
    NSLog(@"acceptsFirstResponder");
    return YES;
}

- (BOOL)resignFirstResponder {
    NSLog(@"resignFirstResponder");
    [self setNeedsDisplay:YES];
    return YES;
}

- (BOOL)becomeFirstResponder {
    NSLog(@"becomeFirstResponder");
    [self setNeedsDisplay:YES];
    return YES;
}


----

What exactly are you trying to draw?

----

Right now just a line, but when the view is redrawn after becoming first responder it leads to the problem I'm having.

----

I think there's something fundamental going on here you're not telling us, since many (all?) of us haven't seen this before.  Can you post your whole project?

----

Here's the deal. I have an NSView that draws the blue frame and the rounded gray frame. I have another NSView sitting right on top of it, a little smaller than the gray frame. Whenever drawRect: is called after becoming first responder, it does the screwy drawing. I have all the code in the drawRect: commented out and it still does it.

----

Now we're getting somewhere.  Are these two frames siblings (both subviews of the same parent view), or are they nested (the gray frame is a subview of the blue frame, which is a subview of the window's content view)?   If the former, that's not supported.

----

I have an NSWindow subclass for the borderless window. I have an NSView subclass for the blue frame and the rounded gray frame. I have another NSView subclass that is the rectangle that is slightly smaller than the rounded gray frame. I have no siblings of another view, and am not setting the windows content view.

----

Show us the code you're using to set up the views. (or post the whole project.  If we could see the whole thing, we could solve this a whole lot quicker)

----

That's the thing, I cant show you the whole project. Here is the code for the funky view:

    
@implementation TempView

- initWithFrame:(NSRect)rect 
{
    [super initWithFrame:rect];
    NSLog(@"initializing view");
    
    return self;
}

- (void)drawRect:(NSRect)rect {
    /*
    NSBezierPath *path = [[NSBezierPath alloc] init];
    [path setLineWidth:1];

    [path moveToPoint:NSMakePoint(10,10,)];
    [path lineToPoint:NSMakePoint(10,20,)];
    [path stroke];
    */
}

- (BOOL)acceptsFirstResponder {
    NSLog(@"Accepting");
    return YES;
}

- (BOOL)becomeFirstResponder {
    NSLog(@"Becoming");
    [self setNeedsDisplay: YES];
    return YES;
}
- (BOOL)resignFirstResponder {
    NSLog(@"Resigning");
    [self setNeedsDisplay: YES];
    return YES;
}

@end



----

Sorry, but to my inexpert eyes, the above looks like fairly benign, almost boilerplate code.

One question: When you close your path, don't you simply stroke a line back to your origin on top of the lineToPoint you just stroked?
Not that there's any law against doing that... but there are other ways to return the pen to your origin.

Is it conceivable that the code in the superview is what is causing the problem?
That is, the code that you can't/won't show us? (Though your desire to protect your intellectual property is not to be questioned.)

You say yourself in your initial query that all is well until you make the subview the First Responder.
Well, that doesn't happen in the above code. It (presumably) happens in your superview.

If I'm OTL here, then we're both going to learn something.

----

Can you boil it down to just a test case, with nib file.  I have a feeling we're talking in circles here and are not getting the whole story.

----

How exactly are you setting the view as the first responder?

----

The methods in the code above are called automatically

Any hope of getting a test case we can look at?

----

**And yet another:**

I've been having a LOT of problems with NSTableView lately, and not in a programming sense, just normal display stuff.  For example, when you move a column, the little hand becomes clenched, but does not then become unclenched.  I occasionally have trouble with little redraws, such as a sliver of dark blue being left by the table when it should be grayed since it is no longer responding.  Also, after putting a tableview inside of a NSSplitView, the results are horrible with aweful redraw problems.  Now, these things work well in other programs, as well as when I just try running test table views in interface builder, which makes me very confused, because loading the same exact nib in a program will cause errors.  Is this my fault?  Is there anything I can do about it?  ANY help is greatly appreciated.

Here are some pics of the split view problem (I don't much care about fixing this anymore since I dropped the split view, but just to give you an idea.  I would much rather have the clenched fists fixed)

http://users.adelphia.net/~ftolmasky/problems/problem1.jpg
This is me resizing a split view.
http://users.adelphia.net/~ftolmasky/problems/problem2.jpg
And once again, same problem

----

**W**'indowAppearanceProblem**

I am having a baffling problem whose solution will probably be quite obvious to everyone except me!

My application looks totally different on 10.2 than 10.3 and I don't want that.  

The window is a transparent window created with

    NSWindow* result = [super initWithContentRect:contentRect styleMask:NSBorderlessWindowMask backing NSBackingStoreBuffered defer:YES];
    [result setBackgroundColor: [NSColor clearColor]];

Various regions of background are filled in with
    [[[NSColor lightGrayColor] colorWithAlphaComponent:1] set];
    NSRectFill([theBox bounds]);

I get the anticipated appearance in 10.3, but when I run the same program in 10.2 I can't seem to fill in gray.  I always get that currugated OS X texture (see picture below)

http://jeffbrent.com/oddwindowappearance.jpg

I've gone through all the info on NSWindow I can find, but must be overlooking the setting or whatever I need to let me just draw a plain gray background and support 10.2 also.

