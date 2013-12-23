---
layout: page
title: HTTPPOSTtoASPdotNet
---



Hi all. I hope somebody can help me. I'm trying to send an HTTP POST request to an ASP.NET server. I get the following error: Error receiving response: NSError "POSIX error: Connection reset by peer" Domain=NSPOSIXErrorDomain.

Any thoughts/ideas/suggestions?

----

Your POST is probably malformed and the server is following the HTTP protocol by disconnecting you when it receives the malformed request.  PostYourCode so we can see what you're doing wrong, or maybe you should test the request against a non-IIS server and see if it helps you deduce what you're doing wrong.

----

Perhaps you're forgetting to URL encode the values in your post form.  Check out this blog post about sending HTTP GET and POST requests in Cocoa:
http://deusty.blogspot.com/2006/11/sending-http-get-and-post-from-cocoa.html

