---
layout: page
title: ImageCompositing
---



I can draw/composite an image in (or on) a view, but how do I composite an image onto another image?
----

This isn't really that hard. When you want to create a new image using existing images, follow these three simple rules.

    
/*
    1. Don't change the sizes of any source images.
    2. Create a new image to act as your destination image (canvas).
    3. Stick to using     drawInRect:fromRect:operation:fraction: for all of your compositing needs.
*/

NSImage *canvas = [[[NSImage alloc] initWithSize:canvasSize] autorelease];
[canvas lockFocus];
[image1 drawInRect:destRect1 fromRect:srcRect1
        operation:NSCompositeSourceOver fraction:1.0f];
[image2 drawInRect:destRect2 fromRect:srcRect2
        operation:NSCompositeSourceOver fraction:0.5f];
[canvas unlockFocus];


----
Thank you.

Related question:
Seems I was mistaken.  I wasn't drawing to the view as I thought I was.  I was just drawing to my main window.  When I try to -lockFocus on my view I get the error:
"Exception raised during posting of notification.  Ignored.  exception:  lockFocus sent to a view whose window is deferred and does not yet have a corresponding platform window."

Sorry for such a beginner's question, but could someone explain to me the issue?
----
AFAIK, Cocoa windows that are "deferred" do not create a place to draw to ("platform window") until they are first displayed. I would try either displaying the window before drawing in the view, or unchecking "Deferred" in InterfaceBuilder (one of the options for NSWindow). --JediKnil
----
The view is a custom view, placed in its window in IB.  The window it's in has had the 'deferred' checkbox unchecked.  I still get the error.
----
It is extremely rare that you need to manually lock focus on a view and draw into it. Why are you doing this?
----
Well, I guess I don't know how to composite an image to a particular view then.  Is there another way?
----
Normally you would override the view's drawRect: method to do any drawing that needs to be done.
----
Ok, so if I wanted to composite an image onto a view I would have to somehow pass the image to the view - with an NSImage variable in the custom view and an accessor method, for example?  Is that how it's done?
----

Yes, you would have to subclass NSView and add a method     - (void)setImage:(NSImage *)image; and then in     - (void)drawRect:(NSRect)rect; you would do     [myImage compositeToPoint ... ].

*Or, you know, use NSImageView or NSImageCell...*
----
Can someone tell me why this code isn't working?

    
- (void)drawRect:(NSRect)rect
{
	[myImage compositeToPoint: NSMakePoint(0,0)
					operation: NSCompositeCopy];
}

- (void)setMyImage:(NSImage *)image
{
	myImage = [NSImage alloc];
	NSLog(@"gangView - setMyImage");
	[image retain];
	[myImage release];
	myImage = image;
	if ([myImage isValid]) NSLog(@"myImage is Valid");
	[self setNeedsDisplay: YES];
}
@end

myImage is, apparently, valid.
----
*Remove     myImage = [NSImage alloc];. Even if that's not causing the problem, it'll still screw up your whole retain-release. --JediKnil*

----

why do you have all that convoluted code? just do
    
- (void)setMyImage:(NSImage *)image
{
	NSLog(@"gangView - setMyImage");
	if (image != myImage)
        {
            [myImage release];
            myImage = [image retain];
        }
	if ([myImage isValid]) NSLog(@"myImage is Valid");
	[self setNeedsDisplay: YES];
}

----
1)  I removed the     myImage = [NSImage alloc]; and, of course, it didn't solve the issue.  I thought I needed to alloc any new NSImage instance?  Another gap in my understanding.

2)  Why all of the "convoluted" code?  I'm new to this an not particularly clever.  ;-)
----
I didn't write the convoluted comment, and don't really agree with it -- although assignment is often combined with retain. However, the reason why you don't call     [NSImage alloc] is because you aren't *creating* an image, you're copying it. In fact, you might want to use     myImage = [image copy] instead of     myImage = [image retain], because otherwise the image might be modified by someone else's code.

It doesn't really look like the problem is in the code you posted, unless your view has a flipped coordinate system. (If it's a subclass of NSTextView, it probably is, and if you overrode     -isFlipped to return     YES, it definitely is). In that case, you don't want to use     NSMakePoint(0,0). Instead, you'll need to find the height of your view and use that for your y-coordinate. --JediKnil
----
Well, I still can't figure out why it's not working.  I'm able to draw text and vector shapes to my custom view, however compositing the image doesn't seem to work (see above code).  If anyone happens to have any more insight as to what I'm forgetting or doing wrong, I'd be much obliged.
----
Can you tell us a little more about what "not working" means? Is it just that nothing shows up? If that's it, I'd take a look at the code where you load the image and pass it into setMyImage:. One test you could do would be to create an instance of NSImageView in your nib alongside your custom view, and then in your code pass the same image to both the NSImageView's setImage: method and your custom view's setMyImage: method. -CS
----

