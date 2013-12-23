---
layout: page
title: SecureConnectionWithCFStream
---



I'm using AsyncSocket to do some custom networking in my application.  I want to add support for secure connections via TLS, but can't get it to work.  I was under the impression that all I had to add was the following:

    
// This method gets called prior to opening the stream
- (BOOL)socketWillConnect:(AsyncSocket *)sock
{
	CFReadStreamSetProperty([sock getCFReadStream], kCFStreamPropertySocketSecurityLevel, kCFStreamSocketSecurityLevelNegotiatedSSL);
	CFWriteStreamSetProperty([sock getCFWriteStream], kCFStreamPropertySocketSecurityLevel, kCFStreamSocketSecurityLevelNegotiatedSSL);
	
	return YES;
}


I wanted to test it to see if it works, so I slapped together a tiny little test to download a webpage.  It was pretty easy after reading this blog post:
http://deusty.blogspot.com/2006/12/statefull-downloading.html

First I tested it by downloading regular (http, not https) webpages.  Everything worked perfectly.  But when I try to get the paypal homepage (https:www.paypal.com - port 443) I always get an error message.  More specifically, a 302 status message, with this in the header: Location = "https://www.paypal.com/mod_ssl:error:HTTP-request";

Everything LOOKS like it's working correctly, but I'm not able to connect to any https servers online.  Am I missing something?

----

302 is not an error code. It's a redirect code (see http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html):

*
10.3.3 302 Found

The requested resource resides temporarily under a different URI. Since the redirection might be altered on occasion, the client SHOULD continue to use the Request-URI for future requests. This response is only cacheable if indicated by a Cache-Control or Expires header field.
*

AFAIK a web client should follow a location header and try and load the URL pointed to by the location field.
I've no idea how this is handled by CFStream.

Hope this helps,
--CristianDraghici

----

Yes, I'm aware of what a 302 status code means.  Which is why I provided the Location header as well, which includes "mod_ssl:error:HTTP-request".  This isn't a redirect.  Because trying to load this page also results in a 302 status code, with the exact same Location header.  In fact, this is the exact same response I get if I try to load the page without configuring any secure connection what-so-ever.  This leads me to the conclusion that I'm not getting the SSL connection right.

So my initial question remains: Am I missing something?

----

Well, it is a redirect, but one likely caused by some error in your request which is confusing Apache.  I'm not sure what you're doing, but if I use openssl as a manual client, and provide the following request, everything works for me:

    
GET https://www.paypal.com/ HTTP/1.1
Host: www.paypal.com


----

Your problem may be that the AsyncSocket delegate method is called onSocketWillConnect: and not socketWillConnect:

:)

This would explain your 302 status code, because security is not being configured on the streams if this method isn't being called.
Also, as a side-note, that blog tutorial was very informative...however...it depends on a Content-Length header.
Doing a quick "curl --verbose https://www.paypal.com" in the terminal reveals that this particular site uses chunked transfer encodings, and thus doesn't have a Content-Length header.

Not sure if you care about this, since you said you were just downloading the page for testing purposes.  If you do, there is a good explanation of how you would go about parsing the HTTP body here: http://www.jmarshall.com/easy/http/#http1.1c2

----

Wow.  I'm an idiot.  (Although, I have no idea how I messed that up.  I swear I copied and pasted from previous working code)

Thanks for pointing out my mistake.  It fixed the problem.

