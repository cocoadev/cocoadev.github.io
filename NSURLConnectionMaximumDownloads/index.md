---
layout: page
title: NSURLConnectionMaximumDownloads
---

I'm using NSURLConnection's sendSynchronousRequest:returningResponse:error: in a thread to download data repeatedly, and it seems as if only about 25 of the first requests passed to this method work, and after that it returns an NSError of "can�t connect to host" even though they work fine in a browser. Is there a certain limit to how much NSURLConnection can handle in a row?? Or is this a bug? I'm able to reproduce it easily.

Actually I found out it is returning an error after the first 25 or so consecutive downloads no matter what class I use - NSURLConnection, NSURL, or CURLHandle. I'm loading this all from a thread, but I don't think that's the problem. Anyone have any clue why this would happen?

Would there be some type of network buffer being filled or something along those lines? 

----

The same problem with WebKit. Any solutions?

