---
layout: page
title: MonitoringUploadToServer
---

I'm trying to use URL Loading System to upload some files to remote server via HTTP protocol.
I would like to receive periodic notifications during upload about the amount of already uploaded bytes.
In other words, I would like to see a progress of uploading process.

----

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSURLConnection.html


NSURLConnection�s delegate methods allow an object to receive informational callbacks about the asynchronous load of a URL request. Other delegate methods provide facilities that allow the delegate to customize the process of performing an asynchronous URL load.�

- (void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data

The delegate receives this message as connection loads data incrementally. The delegate should concatenate the contents of each data object delivered to build up the complete data for a URL load.


----
Thanks for link, but I read it already.
These callbacks work during loading data FROM server. I need callbacks when I'm sending TO server.
I made a couple of tests: sent 1MB to server and received response from server. 
And didReceiveData was called only once. Then connectionDidFinishLoading.

Correct me if I'm wrong.

----

I'm writing a client/server application, where the client is a Cocoa app.  It occasionally sends files to the server via HTTP POST.  The server then sends some sort of acknowledgement as its response. How should I display "upload percentage complete" information to the client?  One way might be to "chunk" my upload, but that seems like it would take a lot of extra overhead.  Another way: send separate occasional messages from the client to the server which ask the server to report the status.

----

Your best bet would probably be to use something other than NSURLConnection. I believe that the [[CUrl]] wrappers will report upload status.

----

more discussion of uploading in HTTPFileUpload

