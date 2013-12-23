---
layout: page
title: OpenNewBrowserWindow
---

I am constructing some HTML and putting it into a WebView with     -loadHTMLString:baseURL:, as shown below. I would like the links in the view to open in a new window of the user's default browser, however, I can't figure out quite how to get it to work. I'm sure there is an easy solution to this - but I'm a little stuck as to what it might be.

    

NSString * header = [ NSString stringWithFormat:@"<html><body>%@", [ selectedObject title ]];
NSString * footer = [ NSString stringWithFormat:@"<br /><br />&raquo; <a href='%@'>More information</a></body></html>", [ selectedObject link ]];
NSString * webString = [ header stringByAppendingString:[ selectedObject content ] ];
webString = [ webString stringByAppendingString:footer ];

[ [ webView mainFrame ] loadHTMLString:webString baseURL:nil ];



As it stands, the link opens in the WebView, rather than in a new window. I have tried adding a "target" to the <A> link (_blank etc), but that leaves the link inactive. Any ideas or thoughts would be much appreciated!

~ TheMZA

----

You need to give your WebView a delegate by doing     -setUIDelegate:, and then you need to implement a method to catch links that open in new windows, like (I think)     -webView:createWebViewWithRequest:. From there, you should be able to extract the URL from the NSURLRequest and then use NSWorkspace to open it in the user's default browser.

----

Thanks - that was really helpful in pointing me in the right direction. I had some problems receiving a valid NSURLRequest using the UI delegate     -webView:createWebViewWithRequest: method, so instead set a WebPolicyDelegate and implemented     webView:decidePolicyForNewWindowAction:request:newFrameName:decisionListener: like this:

    
- (void)webView:(WebView *)sender decidePolicyForNewWindowAction:(NSDictionary *)actionInformation request:(NSURLRequest *)request newFrameName:(NSString *)frameName decisionListener:(id<WebPolicyDecisionListener>)listener {
	[ [ NSWorkspace sharedWorkspace ] openURL:[ request URL ] ];
}


It works nicely - however - the first clicked link from within my app opens Safari (my default browser) and puts the correct URL in the location text box, but does not load the content. You have to manually go to Safari and press return to get it to load the page. All subsequent links open correctly. Any thoughts?

~ TheMZA

----
I am using the NSWorkSpace openURL and it launches and opens the page for me just fine from what I remember. Maybe its a Safari bug? What URL are you trying to load?

--MaksimRogov

----

It looks like this could be a bug in Safari. I'm just loading URLs of normal web pages, however, sometimes I see the strange behaviour detailed above, and sometimes  it works perfectly. Odd.

~ TheMZA

