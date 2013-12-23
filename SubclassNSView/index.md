---
layout: page
title: SubclassNSView
---

I created a custom class from General/NSView and did all my drawing in drawRect.  Also, inside this class I export the drawing to pdf and gif.  I currently have the custom view inside a window.  Here is my question:  Since I just need to render my drawing to pdf and gif, is there a way to accomplish this without displaying the view.  Aslo, why does my gif have a gray background when I already set to white.  Thanks so much.
----
Have you heard of General/NSImage?

----

General/NSImage cannot create a PDF out of non-PDF data (such as arbitrary drawing commands). A view can, so the questioner is on the right track.

The method - dataWithPDFInsideRect: works whether or not the view is visible, in a window or whatever. So all you need to do is instantiate the view, call this method to get the PDF data, then discard the view.Don't know about the gray GIF background. --General/GrahamCox

----

Thanks!  With your suggestion,  I instantiated the view and in my controller class I called the following method but I could not have the view to redraw itself 

Method in custom view class

-(BOOL) generateImageToPath:(General/NSString*)fPath withDataOject:(id) dataObj

{

	[self setDataObj:dataObj];	//dataObj has data for the drawing, which is used in drawRect 

	[self display];	//This does not trigger drawRect as expected

	General/NSData * pdfData =[self dataWithPDFInsideRect:General/NSMakeRect(0,0,442,height)]; //height is calculated in drawRect 

						//If I just specifies a number for height, it works just fine

						//but I don't have exact height until I redraw with the specified data


	if([pdfData writeToFile:fPath  atomically:YES])
	{

		return TRUE;
		.
		.
		.

	}


	return FALSE;	

}

So don't I have to display the view in order to use [self display] or [self setNeedsDisplay:YES] ?

----

dataWithPDFInsideRect will trigger drawRect - you don't have to call setNeedsDisplay or call display or anything at all. Regarding the size though, I suggest you set the size of the view frame at initWithFrame, then draw to fill it (if that makes sense for your application). Then you can just pass [self bounds] in the rect parameter. It's not necessary - but it keeps the size definition in one place. If you can't know height until you've drawn, you might wish to look at a way of precalculating it so that you can set the view up to the right size. I'm assuming that this is down to the extent of the drawing - if you can't precalculate, you risk either clipping the drawing off or ending up with too much white space. Factoring your code so that you can find out this value ahead of time will be worth the effort. --GC


I trully appreciate your knowlege and patience.  I finally know why dataWithPDFInsideRect didn't trigger drawRect the way I had it:  Because height was initial 0, the rect I passed in would not be valid until rawRect have been triggered.  You are right: I need to come up with a good way to precal the height without repeating the algorithm in drawRect.  In the mean time, I will just use dataWithPDFInsideRect twice - first time with the view bounds and second with the exact size.
