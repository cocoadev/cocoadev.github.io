---
layout: page
title: WebKitPluginQuestion
---

I'm trying to learn how to make plugins for General/WebKit apps, namely
Safari.  Using the General/WebKitMoviePlugin example from here as a starting
point:

http://developer.apple.com/documentation/General/InternetWeb/Conceptual/WebKit_PluginProgTopic/Tasks/General/WebKitPlugins.html

Then I want to trigger a javascript method, I added the js to the page
but am struggling with how to call this.  I added a few lines noted by
a comment below.  It's pretty basic but so far I've utterly failed at
get access to the General/WebView's scripting environment.  Help!  I'm a bit
of a Cocoa newbie and a total General/WebKit newbie.

    
- (void)webPlugInStart

{
   
if (!_loadedMovie) {

       _loadedMovie = YES;

       General/NSDictionary *webPluginAttributesObj = [_arguments objectForKey:General/WebPlugInAttributesKey];

       General/NSString *General/URLString = [webPluginAttributesObj objectForKey:@"src"];


               // added these lines before the if statement
               General/NSDictionary *webPluginContainerKey = [_arguments objectForKey:General/WebPlugInContainerKey];

               myWebView = General/webPluginContainerKey webFrame] webView];

               [myWebView evaluateWebScript:@"pluginLaunchSuccess()"];


       if ([[URLString != nil && General/[URLString length] != 0) {

           NSURL *baseURL = [_arguments objectForKey:General/WebPlugInBaseURLKey];

           NSURL *URL = [NSURL General/URLWithString:General/URLString relativeToURL:baseURL];

           General/NSMovie *movie = General/[[NSMovie alloc] initWithURL:URL byReference:NO];

           [self setMovie:movie];

           [movie release];

       }

   }

   [self start:self];
}

