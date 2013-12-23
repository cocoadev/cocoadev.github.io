---
layout: page
title: GetResponseWithoutDownloadingDataWhileWhileWaiting
---

Hi! Help is highly appreciated. I'm stuck here:

I need to examine a response before I start loading an URL in my UIWebView. However a synchronous NSURLConnection will first download the data and then let me access the response, which is bad.

An asynchronous NSURLConnection will let me examine the response before any data is received but I only can only examine the response in the delegate method connection:didReceiveResponse: . But then I would have to tell my webView's delegete method (webView:shouldStartLoadWithRequest:navigationType:) to wait till the the response is there. Because both delegates are in the main thread (do they have to be? I guess so) I can't simply use NSCondition to stop webView's delegate (cause that means stopping the main thread).

Does anyone have an idea on how to solve this problem?

Thanks in advance.

