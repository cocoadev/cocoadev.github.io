---
layout: page
title: NSURLDownloadingWrongPage
---

I have been stuck on this problem all day and now all night. I am creating a NSURL object to "http://xoap.weather.com/search/search?city=atlanta".

At first this seemed like a simple task, but for some reason what is downloaded is from the url "http://www.weather.com/search/search?city=atlanta".

I have downloaded other xml data and other urls with queries contained in them with no problems. I have also checked to ensure that all the characters in my url string are eligible to make a url. Can someone please help? I am using OS X 10.1.5 and this is the code I am trying:

NSURL *url = [NSURL URLWithString:@"http://xoap.weather.com/search/search?where=atlanta"];

NSString *response = [NSString stringWithContentsOfURL:url];

NSLog( @"%@", response );

[response writeToFile:@"Response.html" atomically:NO];

----

maybe it is just redirected to the the new URL...

----

I'm not sure what the problem is, but all the URL downloading problems I had disappeared when I switched to CURLHandle (http://curlhandle.sourceforge.net ); it's pretty much a drop-in replacement for NSURLHandle and works about a kojillion times better.  With the Safari 1.0 release, NSURL now uses the same download code as Safari and works much better as well, but that doesn't help you back on 10.1.  --Bo

----

Does NSURL set the Host header? It's possible xoap.weather.com and www.weather.com resolve to the same IP address, and are implemented as name-based virtual hosts. I there is no Host header in the request, the web server will display the default (which would generally be www).  --Sam

----

Not my issue but I did check xoap.weather.com and while it does have a different IP as www.weather.com, it does give you a normal web page of not given the search criteria.
i.e.: **search/search?where=atlanta**

----

Hmm. Occasionally some brain-dead web servers will still stuff this up if they're hosted on the same box, but are not configured to use address-based virtual hosting (ie each host listens on all available addresses). I'm pretty sure NSURLRequest in WebKit adds a host header automatically for 'http://' URLs; I couldn't tell you whether NSURL does or not. Is there any way to add http headers? 

Actually, netcraft says they're running apache on linux (I take back the brain-dead bit :) ). It's still possible to configure Apache this way, just more difficult to do it by accident. --Sam
----
I looked at NSURL with this example a little bit more, and the more I look at it, the more I am convinced that it's just broken. After creating it with this url, you cannot get any parameters out of it, such as **parameterString**, etc...

----

The stuff after the question mark is the     query not the     parameterString.-- Bo

