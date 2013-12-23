---
layout: page
title: GeneratingSimplePDFs
---

I'm developing an app that needs to be able to generate reports in PDF format. What would be the easiest way to do this? I tried taking a look at Tiger's PDFKit, but apparently that's only to view PDFs, am I missing something? If not, then what would be the easiest way to do this? Thanks in advance.

*Well, you could use the Cocoa printing architecture...*

As noted above, the Cocoa printing architecture will output pdf, so anything that can be rendered into a view can be fairly easily printed and saved to pdf. Couple web WebView, it's pretty easy to desigh reports in html, render in WebView, then output to pdf. I've done quite alot of this; a product I'm working on uses html for all report generation, using WebView for render and output.

Mike Ross

*I do this also. Quite simple to build an HTML templating system using NSString and the CSS of your choice, shove it into a WebView, and laugh all the way to the bank. -- RobRix*

----

How do I create an HTML document within Cocoa and then how do I display that in a WebView?

Build your html as an NSString these set it to your WebView with:
    
m_webView mainFrame] loadHTMLString:m_theHtml baseURL:nil]


----

I managed to get this working, but I'm having a few problems. First, I'm trying to save the contents of my [[WebView right after I tell it to load the HTML from my string the problem seems to be that the print operation takes place before the WebView is finished loading my HTML. Also, once the PDF is generated it's the size of my WebView rather than the size of an A4 page, and when the content is too long it does not paginate, it just creates a huge PDF. Here's the code I'm using:

    
webview mainFrame] loadHTMLString:htmlString baseURL:nil];
[[NSFileManager *filemanager = [NSFileManager defaultManager];
NSMutableData *data = [NSMutableData data];
NSPrintInfo *info = [NSPrintInfo sharedPrintInfo];
[info setPaperSize:[NSPrintInfo sizeForPaperName:@"A4"]];
NSPrintOperation* operation = [NSPrintOperation PDFOperationWithView:[ventana mainFrame] frameView] documentView] insideRect:[[[[ventana mainFrame] frameView] documentView] frame] toData:data printInfo:info];
[operation runOperation];
[filemanager path contents:data attributes:nil];


I'm using [[PDFOperationWithView:insideRect:toData:printInfo: because when I tried using PDFOperationWithView:insideRect:toPath:printInfo: with the path being the exact same one as the one I'm using with the filemanager, the PDF file was not being created.

Nevermind, I got it working using the same method used in Cocoa Dev Central's PDF tutorial. http://cocoadevcentral.com/articles/000074.php

