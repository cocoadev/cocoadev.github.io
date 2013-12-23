---
layout: page
title: WebKitSharewareRegistration
---

Apple's posted a tutorial on using General/WebKit to register an application online from within the app:

[http://developer.apple.com/internet/webservices/webkitreg.html]

What does everyone think of it? Is it better or worse than just pointing the user's browser at a URL?

**Pro**

* More control over registration environment
* Better integrated into the application's UI


**Con**

* Users may be uncomfortable with security issues - "I trust Safari, but what is this app doing with my data?"
* More code (both client & server) to maintain and debug

----
I use eSellerate with my applications. One of the reasons I chose eSellerate is because they offer an integrated seller, which is basically like what the Apple article is talking about except that it uses a custom interface, not a web page. I thought that the ease of this system would be good for people, since they can buy the software without switching to another program, and once the purchase goes through the serial number is delivered directly to my application. I also kept eSellerate's normal web store around, in case people wanted to use it for whatever reason. About 80% of my purchases have been through the integrated system, so I guess I was right. I haven't had any complaints about the system so far. The "more code" downside doesn't apply, of course, because it's eSellerate who maintains it, and interfacing with their code just takes a few lines.

In conclusion, go for it if it's worth the time it takes to implement it, but provide a "traditional" web store as well.
