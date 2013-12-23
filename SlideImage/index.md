---
layout: page
title: SlideImage
---

I had gotten my hopes up about using NSWorkspace's slideImage:from:to: method to provide visual feedback to a user when they click a button to add an object to a list, rather than drag it. I wanted to use the same image as the one I compose as my drag image. Basically, I want to slide the drag image from tableViewA to tableViewB without DnD when a button is pressed.

Unfortunately, the devil is in the details. The last line in the docs said "Currently has no effect." Sure enough, the docs are accurate. ;-)

The question is, how can I achieve this on my own? I would assume I'd need to:


* Get the screen coordinates of the source row of the source tableview.
* Get the screen coordinates of (maybe the middle of) the target tableview.
* Compose the drag image.
* Perhaps create a custom view and draw the image to it (since both tableviews are in the same window).
* Move the custom view from point A to point B, maybe play a 'swish!' sound, then maybe the poof animation.
* Perform the actual action on the data model and update the views.


Is there a better way to do this? Any suggestions? Performance issues that I'm likely to run into (though the drag image is only maybe 100x32, made from a 32x32 icon and an attributed string label)?

For the purists among you, yes, I fully intend to make this gratuitous flying icon option a prefs option. The intent is to dazzle, but not annoy. ;-)

Thanks!

 - JNozzi

----
Just create a window and slide that...

    

- (void)awakeFromNib
{
    [slidingWindow setDelegate:self]; // for windowDidMove
    // ...
}

- (void)whatever
{
aRect = [tableA rectOfRow:interestingRow];
aRect = [tableA convertRect:aRect toView:nil];

bRect ...

    [slidingWindow setAlphaValue: 0.5]; // +/- a tad
    [slidingWindow setFrame:aRect display:YES];
       [imageViewInSlidingWindow setImage: yourSlidingImage];
    [slidingWindow orderFront:nil];
    [slidingWindow setFrame:bRect display:YES animate:YES];
}


- (void)windowDidMove:(NSNotification *)aNotification
{
NSRect sRect = [slidingWindow frame];

  if (NSEqualRects(sRect, bRect)) [slidingWindow orderOut:nil];
}


Taking this basic idea you could write a NSWindow subclass that does most of this for you kind of like the ToolTip classes.

----

Sounds like a job for NSViewAnimation! This tutorial might help: http://andymatuschak.org/articles/2005/12/12/nsanimation-for-fun-and-profit

-- Andy Matuschak

----
NSViewAnimation is more flexible but requires Tiger+. A window based solution should be good all the way back. I'd personally go with NSViewAnimation but I've got no market constraints...being a hobbyist has its advantages, though they aren't monetary.

----
It would be a neat project to reimplement NS[View]Animation's interfaces in Panther-compatible code. Maybe I'll do that sometime. -- AndyMatuschak

