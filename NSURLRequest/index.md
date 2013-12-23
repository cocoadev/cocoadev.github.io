---
layout: page
title: NSURLRequest
---

NSURLRequest sets additional behaviour to a to be loaded NSUrL, like timeout, use of cache and headers.

as example request a URL with a specific HTTP header tag:
(MiniBrowser sample)
    
 NSMutableURLRequest *urlRequest;
 urlRequest = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"myURL"]];
 [urlRequest setValue:@"myURL" forHTTPHeaderField:@"Referer"];
 [[webView mainFrame] loadRequest:urlRequest];


http://www.w3.org/Protocols/HTTP/Request.html
http://www.w3.org/Protocols/HTTP/HTRQ_Headers.html

