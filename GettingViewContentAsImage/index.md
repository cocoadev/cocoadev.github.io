---
layout: page
title: GettingViewContentAsImage
---



I'm trying to find out if there's a way to get the contents of a view (only the contents, not the whole view) as an image. Specifically I'd like to get the rendered content of a webview as an image (and since a WebView is also an NSView, I assume this can be applied to views in general).

I've tried a few things, like getting a bitmap or PDF representation of the view, but the image always ends up looking exactly as the view is displayed onscreen, and not just its contents.

----

you need to get the representation from the interior view, not the scrolling view. for a WebView, that'd be      [webView mainFrame] frameView] documentView]

I've already tried that, and it doesn't make any difference - it's as if it gets the whole view, not just the content. Say if the content is 300px wide, but the view is 800px wide, when I get an image, even using that method, I still get something that looks like how it's displayed onscreen, and not just the 300px wide content.

* if you want the content to be a different size, make a new view of that size, render the content into there, and grab your image.*

[snide comments truncated]
----

I don't think there's a way to determine the size of the contents, so it's probably not possible to resize it to what you need unless you already know what size you need, which would make the whole question pointless.

I'm guessing you want something much like how it would be printed out or saved to PDF; have you tried to run an [[NSPrintOperation on the view, saving it to file, and using that as the image?

* I did try something like that, but didn't go into details with the printInfo in my experiments so was getting much of the same results. I'll play around with adding options and trying again, as this is probably the only way I'll get the closest representation of what I want.*

----

After looking a bit at the WebKit private headers, I've determined that one **can** get pretty much just the (scaled) content of a webview (and webview only), and only as a printOperation. The following code acomplishes this; the trick is to save the operation to file, and create an image from that file.
    
-(NSImage *)createImage:(WebFrame *)frame
{
	// create thumbnail of the page
	
	NSImage *image;
	id docview=frame frameView] documentView];
	
	// make sure prefs set to print bg
	BOOL bgSetting=[[[frame webView] preferences] shouldPrintBackgrounds];
	[[[frame webView] preferences] setShouldPrintBackgrounds:YES];

	[[NSPrintInfo *pi=[NSPrintInfo sharedPrintInfo];
	[pi setJobDisposition:NSPrintSaveJob];
	pi dictionary] setObject:@"/path/to/save.pdf" forKey:[[NSPrintSavePath];
	pi dictionary] setObject:[[[NSNumber numberWithInt:1] forKey:NSPrintFirstPage]; // only want a preview
	pi dictionary] setObject:[[[NSNumber numberWithInt:1] forKey:NSPrintLastPage];  // so only first page
	pi dictionary] setObject:@"Letter" forKey:[[NSPrintPaperName];  // desired image size
	[pi setBottomMargin:0.0];
	[pi setTopMargin:0.0];
	[pi setLeftMargin:0.0];
	[pi setRightMargin:0.0];
	
	NSPrintOperation *op=[NSPrintOperation printOperationWithView:docview printInfo:pi];
	
	[op setShowPanels:NO]; // don't show panels
	
	[op runOperation];
	
	// restore webview preferences setting
	[frame webView] preferences] setShouldPrintBackgrounds:bgSetting];
	
	// get the image as data, and resize appropriately	
	image = [[[[[NSImage alloc] initWithContentsOfFile:@"/path/to/save.pdf"] autorelease];
	[image setScalesWhenResized:YES];
	[image setBackgroundColor:[NSColor clearColor]];
	[image setSize:NSMakeSize(200,260)]; // max size we want, with a letter size ratio
	return image;
}


Takes a few seconds to respond the first time, but subsequent calls are a bit faster. Don't know if there's a way to speed that up.

-Seb

----

The problem with doing the NSPrintOperation is that it uses the print stylesheet.  If you want the view as rendered on-screen, use the following  (this should work for any view):

    
- (NSImage *)createImageFromView:(NSView *)view {
	NSRect rect = [view bounds];
	NSBitmapImageRep *imageRep = [view bitmapImageRepForCachingDisplayInRect:rect];
	[view cacheDisplayInRect:rect toBitmapImageRep:imageRep];
	NSImage *image = [[[NSImage alloc] initWithSize:rect.size] autorelease];
	[image addRepresentation:imageRep];
	
	return image;
}


To use this with a webView, pass it [webView mainFrame] frameView] documentView]

Note that this will be problematic if the web view uses plugins. At present I don't know of a way to capture a complete image of a [[WebView that contains plugin content.

----

I had something almost identical to the above code trying to create an NSImage of view, but kept getting weird artifacts in the final image. It turns out you need to clear the initial bitmap that you are handed!

    
- (NSImage *)createImageFromView:(NSView *)view {
	NSBitmapImageRep *imageRep = [view bitmapImageRepForCachingDisplayInRect:[view bounds]];
	unsigned char *bitmapData = [imageRep bitmapData];
	if (bitmapData != NULL) bzero(bitmapData, [imageRep bytesPerRow] * [imageRep pixelsHigh]);
	[view cacheDisplayInRect:[view bounds] toBitmapImageRep:imageRep];
	NSImage *image = [[NSImage alloc] init];
	[image addRepresentation: imageRep];
	
	return image;
}


- Jeffrey J. Early

----

