---
layout: page
title: DOMDocument
---



Root node for a WebFrame's NSURLRequest.

The DOMDocument can also be used to modify the content of a page in the WebFrame programmatically.

WebKit provides Objective-C access to the Document Object Model, the object-like structure of the HTML contents of a WebFrame. Using essentially the same methods used in Javascript to access and alter the DOM contents of a page, methods in DOMCore.h (and others) allow altering, adding and removing tags and text contents, creating new elements, finding elements by ID, etc.

**Issue and workaround:** Some changes via this method are not seen until some external event such as resizing the webview or mousing over it (seen on 10.4.10/Safari 2.0.4, whatever WebKit version that is). For one, changing the style attribute of the BODY tag. I found an old (2005) Safari Javascript workaround to force the redraw (without reloading the page). Here it is in Objective-C:
    
NSUserDefaults *defs = [NSUserDefaults standardUserDefaults];
NSString *family = [defs stringForKey:GSMsgFont];
DOMDocument *doc = _myWebView mainFrame] [[DOMDocument];
DOMHTMLElement *mybody = (DOMHTMLElement *)[doc getElementById:@"gsge1"];
// Notice the unusual blank-labeled 2nd argument - this is done to more closely match the DOM method style
[mybody setAttribute:@"style" :[NSString stringWithFormat:@"font-family: %@", family]];
// force redraw
DOMNode *dummy = [doc createElement:@"div"];
[mybody appendChild:dummy];
[mybody removeChild:dummy];

