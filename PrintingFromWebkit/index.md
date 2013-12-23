---
layout: page
title: PrintingFromWebkit
---

I'm a new cocoa programmer experementing with the WebKit.
I have a cocoa application that uses WebKit to display HTML.  I want to be able to print HTML pages from this application.

*Just connect your UI to the     print: action of the WebView*

**I've tried to print the contents of a WebView, but it doesn't fit the pages or do pag ination -- I've searched the WebKit and Cocoa lists, and I find a lot of persons asking how to print the WebView but no real answers.**

**Update: not sure what I did wrong before, but now it works. I'm sending print: to the documentView -- I wonder if there is any way to change the default margins?**

Try this before you send `print:` to the `documentView:`
    
NSMutableDictionary* dict = [[NSPrintInfo sharedPrintInfo] dictionary];
[dict setObject:[NSNumber numberWithFloat:36.0f] forKey:NSPrintLeftMargin];
[dict setObject:[NSNumber numberWithFloat:36.0f] forKey:NSPrintRightMargin];
[dict setObject:[NSNumber numberWithFloat:36.0f] forKey:NSPrintTopMargin];
[dict setObject:[NSNumber numberWithFloat:36.0f] forKey:NSPrintBottomMargin];


I'm  using the technique above to print, but when I print a half page, it's centered.  How do I get it aligned to the top?  Basically, I want to print my WebKit view the same way as Safari.

Solution: [printInfo setVerticallyCentered:NO];

----

I'm trying to create a dump of an entire website into PDF. I have got this far, so far:

    
    NSView *view = [webViewOutlet mainFrame] frameView] documentView];
    if(view)
    {
	[[NSData *data = [view dataWithPDFInsideRect:[view bounds]];
	[data writeToFile:@"webkit.pdf" atomically:NO];
    }


Does any know how to capture the background as well?

SimonRobins

Here's how to print backgrounds...  Put this in your awakeFromNib function.
    
	// Capture Backgrounds when Printing
	WebPreferences *webPref = [[[WebPreferences alloc] initWithIdentifier:@"myIdent"] autorelease];
	[webPref setShouldPrintBackgrounds:YES];
	[webView setPreferences:webPref];


----

On a related topic, how can I capture images produced by Javascript running in a WebView? I've tried both the dataWithZZZInsideRect: methods and only the HTML stuff is rendered. Similarly, the Javascript stuff is missing if I print the page. It's interesting that Grab correctly produces a rendering of the whole page, including the Javscript bits. Anybody know how it does this?

Kevin Patfield

----

Grab works by simply capturing raw pixel data. You can do the same thing as Grab by getting a pointer to your window's backing store and sucking pixels out of it. However, you won't be able to get more than the visible area, and you'll be limited to screen resolution.


----

OK, grabbing data from the backing store sounds like exactly what I need. I really want to find out if a plugin I'm using has changed the content of a window owned by my application. If I could compare an NSData captured before and after, that would be great. But how, please, do I get this pointer to the window's backing store? Thanks.

Kevin Patfield

----

If you want to print headers & footers you can with Safari 3.0 based systems?

Using the UIDelegate methods found at

http://trac.webkit.org/projects/webkit/browser/trunk/WebKit/WebView/WebUIDelegate.h#L534

Specifically use delegates (and their friends for headers)

    
- (float)webViewFooterHeight:(WebView *)sender; /* To reserve space, you need to reserve space or you'll get a 0 height rect!  */
- (void)webView:(WebView *)sender drawFooterInRect:(NSRect)rect /* to do actual drawing */


loghound


----

How do I print Flash content? I can see it in the WebView but it doesn't show up when written using dataWithPDFInsideRect.

