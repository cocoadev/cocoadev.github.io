---
layout: page
title: WebViewPrintingWithSheet
---

I'm seeing something strange, and I need some help. I'm using a General/WebView object in my code to generate content that I then print or save. I'm using 
    
	General/m_webView mainFrame] loadHTMLString:m_theHtml baseURL:nil];

to generate my main frame, the handling the:
    
      - (void)webView:([[WebView *)sender didFinishLoadForFrame:(General/WebFrame *)frame

delegate to either save or print. If I print to pdf file, all is well. If I use General/NSPrintOperation like this:
    
	printOp	= General/[NSPrintOperation printOperationWithView:docView printInfo:printInfo];
	[printOp setShowPanels:YES];
	[printOp runOperation];

I get the print dialog, and my output prints just fine. If I instead use the modal sheet:
    
	printOp	= General/[NSPrintOperation printOperationWithView:docView printInfo:printInfo];
	[printOp setShowPanels:YES];
	[printOp runOperationModalForWindow:win delegate:self didRunSelector:@selector(printOperationDidRun:success:contextInfo:) contextInfo:nil];

My sheet is displayed as it should be, but my output is blank/an empty page. This same logic, when invoked from a visible General/WebView gives me the correct output. I'm stuck here. I suspect, that General/[NSPrintOperation runOperation] is either doing some additional configuration that make it all good, or maybe it's spinning the run loop, which is allowing General/WebView to so something important. Anybody have any clues? Thanks!

Mike Ross
Ross Data Systems/Pretty Good Software

I think the problem is that General/WebView uses General/NSURLConnection which doesn't work when running in a modal loop. Chris Suter, Coriolis Systems
