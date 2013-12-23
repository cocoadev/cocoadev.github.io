---
layout: page
title: ProgramaticallyCreatedNSScrollView
---

In a Cocoa Multi-Document Application I want to create an NSScrollView That will scroll the contents of myDocument window. I can't do this by selecting all of the sub views in Interface builder and using the Layout>Make subviews of>Scroll View because it will only create a frame for the NSScrollView that will be as big as the limits of the frames of the sub views NOT the whole contents of the window. I want to include the white space around the sub views as well. Also I don't want to do it in Interface Builder because once I've made a window sub view a child of an NSScrollView it can be resized but not moved in the NSScrollView. The bottom left corner is locked and can't be dragged or changed in the Inspector. In other words once the subviews are made children of an NSScrollView they are cast in stone and changing the layout of the items is impossible. In MyDocument.h I declare an NSScrollView:
    
@interface MyDocument : NSDocument
{
    NSScrollView* windowScrollView;
}
@end

In MyDocument.m I attempt to create a Scroll View for the window like this:
    
- (void) windowControllerDidLoadNib:(NSWindowController *) aController
{
NSRect myRect = myDocumentWindow contentView] frame];
myRect.size = [[[NSScrollView frameSizeForContentSize: myRect.size hasHorizontalScroller:YES hasVerticalScroller: YES borderType: NSNoBorder];
windowScrollView = [[NSScrollView alloc] initWithFrame: myRect];
[windowScrollView setHasVerticalScroller: YES];
[windowScrollView setHasHorizontalScroller: YES];
[windowScrollView setBorderType: NSNoBorder];
[windowScrollView setAutoresizingMask: NSViewWidthSizable|NSViewHeightSizable];
[windowScrollView setDocumentView: [MYDocumentWindow contentView]];
[myDocumentWindow setContentView: windowScrollView];

[self drawMySubviews];
}


Apple's example in the Scroll View Programming Guide and the documentation for NSWindow tell me to do some retaining and releasing of objects but if I do the application crashes. Anyway all the app draws is a blank window with blank scroll bars. Setting the responder of the window and scrollView doesn't help. How can I make this work?

----

You do realize that there's an NSClipView sitting in between your scroll view and the content view, correct?  It doesn't make sense for your view to be moved around inside the scroll view.
----
Dude! Group whatever views you want in custom view (You can do this in IB) and then put the whole mess in a scroll view.  You can reposition views within the custom view.  Only the custom view is pinned to the corner of the clip view.  Even thinking of abandoning IB for a triviality like this suggests a prior bias that isn't warranted.
----
Yes, I know that, I did it and it was the frustration of trying to make IB put everything where I wanted without playing god and deciding how much wider than my group of objects the custom view had to be, treating horizontal and vertical scrollbars differently, and the hassle of unpacking the subviews before adding another view and setting it up again that made me think that programatically creating a scrollview would be an easier way to deal with it.

----
I would suggest putting your custom view at the top level of the nib, and then creating a placeholder view in a scroll view, all in IB. Then in code you can grab the top-level custom view and insert it into the scroll view. This will give you all of the benefits of using IB but without the layout difficulty that comes with a view embedded in a scroll view in IB.

----
Not at my machine right now, but doesn't NSScrollView expose a documentView outlet in IB?  You wouldn't even need to set the document view in code then.  Just draw it in IB as a top-level NSView and connect it to the outlet.
----
Um, no.  NSScrollView does not expose a documentView outlet in IB.  One line of code is nedded to set the document view of a scroll view.
    
[someScrollView setDocumentView:someView];

----
I have encountered more problems by putting all of the views in myDocument into a custom view and then making it a subview of a scroll view. The custom view has a color and it's slightly opaque. This creates a small but noticable background color in myDocument's window. I don't want this, I want a white (transparent) background in myDocument's window. This is much more noticable when printing. When I call
    
NSView *printView = myDocument window] contentView];

I get a view which contains the slightly opaque custom view which I don't want and the scroll bars, which I don't want. If the window is at full size my program prints the inactive horizontal bar only and clips the right side of myDocument so that some of the content is lost. If the window is smaller and the scroll bars are active I print only the visible part of myDocument's window with the scrollbars. The same is true of saving of course since this also saves the [window contentView] but the saved image of myDocument's window has both scrollbars drawn correctly. What I really need is a custom view to hold all of the subviews, which is an [[IBOutlet that I can refer to for printing/ssaving, right?
----
I solved the scroll bar problem by making the custom view (that groups the document window sub views) that is a child of a scroll veiw an IBOutlet and printing/saving it, not whe window content view. This also solves the opacity issue in printing and graphic images of myDocument but it is still somewhat opaque on my monitor. I added this code to the - (void)drawRect:(NSRect)rect method in the customView code, thinking that since it would be drawn first it would clear the background of the myDocument window:
    
[NSGraphicsContext saveGraphicsState];
CGContextRef myContext = [[NSGraphicsContext currentContext] graphicsPort];
CGContextSetRGBFillColor (myContext, 0, 0, 0, 0);
CGContextFillRect (myContext, CGRectMake (0, 0 , [self bounds].size.width, [self bounds].size.height));
[NSGraphicsContext restoreGraphicsState];

It doesn't help

