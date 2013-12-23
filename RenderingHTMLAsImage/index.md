---
layout: page
title: RenderingHTMLAsImage
---



Is it possible to render HTML directly as an image?  I've been trying to do the following:

    
webView mainFrame] loadRequest:[[[NSURLRequest requestWithURL:myURL]];
NSView* tempView = [webView mainFrame] frameView] documentView];
[[NSData* imageData = [tempView dataWithPDFInsideRect:[tempView bounds]]
[previewFrame setImage:[[[NSImage alloc] initWithData:imageData] autorelease]];


However, previewFrame stays empty.  The peculiarity of this code is that webView is initialized with 'WebView* webView = [[WebView alloc] init];' and is never displayed in the GUI.

So, my question is, is there a way to get this method to work?  Or is there a simpler way to directly render the HTML as an image without all this mucking about with views?

(The reason I want this to be not simply a WebView is that I want links to be disabled and the content to be scalable.)

Thanks.

----

Read the WebView docs. You can customize virtually everything about webpages.

Related to your code, you may need to wrap     dataWithPDFInsideRect: inside calls to lockFocus and unlockFocus on your tempView.

*No, there is no need to lock or unlock focus here. The problem is that loadRequest: is asynchronous; you have to give it time to work. Send that, and set up a delegate to get notified when the load is complete. Once that gets called, then you can grab your PDF and go.*

----

I tried to get this to to work by setting up the delegate, but that didn't seem to work.  So, is there a way to simply render the HTML directly as an image?  Or am I barking up the wrong tree?

----

*Code*, show us your *code*. :-) 

----

Here you go. But it's probably better to use NSBitmapRep's     initWithFocusedViewRect: and generate an NSImage from that instead of a PDF. --KevinWojniak

    
#import <WebKit/WebView.h>
#import <WebKit/WebFrame.h>

- (void)loadImage
{
	// assumes webView is initalized somewhere already...
	[webView setFrameLoadDelegate:self];
	webView mainFrame] loadRequest:[[[NSURLRequest requestWithURL:[NSURL URLWithString:@"http://www.apple.com/ipod"]]];
}

- (void)webView:(WebView *)sender didFinishLoadForFrame:(WebFrame *)frame
{
	NSView *tempView = [sender mainFrame] frameView] documentView];
	[[NSData *pdfData = [tempView dataWithPDFInsideRect:[tempView bounds]];
	[pdfData writeToFile:[NSString stringWithFormat:@"%@/Desktop/test.pdf", NSHomeDirectory()] atomically:YES];
}

