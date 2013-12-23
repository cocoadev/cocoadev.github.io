---
layout: page
title: GettingHTMLSourceInWebView
---

What would be the best way to retrieve the HTML source for a web page in a WebView?

Thanks, and merry Christmas.

----
 
The main frame of the web view is probably the best place to start.

    
- (NSData *)HTMLDataFromWebView:(WebView *)webView {

    WebFrame *mainFrame = [webView mainFrame];
    WebDataSource *dataSource = [mainFrame dataSource];
    NSURLRequest *initialRequest = [dataSource initialRequest];
    NSURL *url = [initialRequest URL];
    DOMDocument *doc = [mainFrame DOMDocument];

    return [dataSource data];

}


The DOMDocument is an object model of the web view's content. Check out DOMCore for more info. --zootbobbalu

----
Why exactly are you creating initialRequest, url, and doc in that example? And why are you returning an NSData object, when he most likely is going to want it as a string?  To anyone copying and pasting that code, it is a little over the top and has three unused variables.

    
- (NSString *)HTMLSourceFromWebView:(WebView *)webView {
    WebFrame *mainFrame = [webView mainFrame];
    WebDataSource *dataSource = [mainFrame dataSource];
    
    return dataSource representation] documentSource];
}

// or, more simply put as:

- ([[NSString *)HTMLSourceFromWebView:(WebView *)webView {
    return [[webView mainFrame] dataSource] representation] documentSource];
}


----

Thanks guys. Yeah, the string is what need the most.


----

*Why exactly are you creating initialRequest, url, and doc in that example? And why are you returning an [[NSData object, when he most likely is going to want it as a string?  To anyone copying and pasting that code, it is a little over the top and has three unused variables.*

I'm trying to give as much info as possible. Of course those variables are doing nothing. I intentionally put them there to enlighten the original poster to the basics of working with WebKit. --zootbobbalu

----
*Reformatted for clarity.*

**January 4, 2005** - The above example retrieves the original document contents. However, if you make the WebView editable and make changes, the above will not retrieve the changed text. The following example will:

    
- (NSString *)HTMLSourceFromWebView:(WebView *)webView
{
    return [[webView mainFrame] [[DOMDocument] documentElement] outerHTML];
}


*The above example uses DOMDocument, which was introduced in Tiger so your app's minimum requirements would become Tiger or above.  Just a note.*

----
How would you get the source from a URL or URL string directly without loading it in a WebView?

