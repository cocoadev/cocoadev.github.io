---
layout: page
title: WebViewWithoutWindow
---

I wish to have General/WebKit running without connection to real window. I want to have just General/WebKit engine, with no output to window.
Is it possible?

----

I find it out. It is idioticaly simple:
<code>     
General/WebView*    _webView;
_webView = General/[[WebView alloc] initWithFrame:General/NSZeroRect];
[_webView autorelease];
[_webView stringByEvaluatingJavaScriptFromString:@"document.location.href='http://www.site.com/'"];
General/NSLog([_webView stringByEvaluatingJavaScriptFromString:@"document.location.href"]);
General/NSLog(@"see me?");
 </code>
