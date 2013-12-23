---
layout: page
title: PrintUsingWebKit
---

Using WebKit to print data can be a lot easier than printing using the AppKit NSView classes.  I used this code to print tabular data from out application and it was much easier than mucking with NSTableView


    

- (void)printShowingPrintPanel: (BOOL)flag
{
	NSPrintInfo *printInfo = [self printInfo];
	[printInfo setTopMargin:15.0];
	[printInfo setLeftMargin:15.0];
	[printInfo setRightMargin:15.0];
	[printInfo setBottomMargin:15.0];
	[printInfo setHorizontallyCentered:NO];
	[printInfo setVerticallyCentered:NO];

	[printInfo setHorizontalPagination:NSFitPagination];
	[printInfo setHorizontallyCentered:NO];
	[printInfo setVerticallyCentered:NO];

	NSRect imageableBounds = [printInfo imageablePageBounds];
	WebView *webView = [[WebView alloc] initWithFrame:imageableBounds frameName:nil groupName:nil];
	[webView setFrameLoadDelegate:self];
	WebFrame *mainFrame = webView mainFrame] retain];

	[[NSMutableString *html = [NSMutableString stringWithString: @"<html><head></head><body>"];
	
	/* Magic to create the HTML you want to print here */
	// load the html into the web frame.  This is asyncronous.
	[mainFrame loadHTMLString: html baseURL:nil];
}

// this delegate is called when the webframe is done loading the HTML string, so lets print from here!
- (void)webView:(WebView *)webView didFinishLoadForFrame:(WebFrame *)frame
{
	NSPrintOperation *printOperation;
	NSPrintInfo *printInfo = [self printInfo];

	printOperation = [NSPrintOperation printOperationWithView: [webView mainFrame] frameView] documentView] printInfo: printInfo];
	[printOperation setShowPanels:YES];
	[self runModalPrintOperation:printOperation delegate:nil didRunSelector:NULL contextInfo:NULL];
}

 

-- [[AdhamhFindlay

