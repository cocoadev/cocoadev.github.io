---
layout: page
title: WebViewNotReloadingPage
---

Some web pages force the browser to reload its contents after a period of time. Is there any way to tell a webview not to reload a page?

----
You could always subclass General/WebView and and override the default reloading function with your own that does the checking (example below).
    
- (General/IBAction)reload:(id)sender {
    // do your check to see if the page reload should happen.
    // assume this variable exists and is defined as a BOOL
    if (!shouldLoadPage) return;
    [super reload:sender];
}

