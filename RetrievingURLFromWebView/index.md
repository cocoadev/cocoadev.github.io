---
layout: page
title: RetrievingURLFromWebView
---



Given a WebView that's loaded (for example) google.com. Let's say I click on maps.google.com. Is there a way to get the URL out of that click when it starts to load? Or even a polling solution would be fine. I just need to be able to query a WebView and have it return its current URL address. Any ideas?

-- bradbeattie@gmail.com

----
Before others go all 'RTFM' and 'Google-is-your-friend' on you, let me provide you with a useful response: what you want to look at is the webView:decidePolicyForNavigationAction:request:frame:decisionListener: method of the WebPolicyDelegate protocol; this will allow you to determine what action occured (like a link click), get the info for that action (what was clicked?), and decide what to do about it (proceed or ignore it). 

If you want to know what URL is currently loaded on a webview's frame, then doing [theWebView mainFrame] dataSource] request] (or initialRequest) will provide you with the [[NSURLRequest that created the main page frame. -Seb

