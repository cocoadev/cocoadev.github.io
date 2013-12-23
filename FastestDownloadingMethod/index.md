---
layout: page
title: FastestDownloadingMethod
---

I think I have found the #1 fastest way to download a file (anything you want). I have been using CURLHandle for a long time, but it started to tick me off and I began to look for better methods. I tested out just the plain ol' built-in initWithContentsOfURL methods for NSData and NSString, but those weren't any better (and you have less control of the download - even with NSURLHandle). So I started looking into NSURLDownload and NSURLConnection - people these are the best methods I believe for downloading. NSURLDownload basically downloads a NSURL to a file while NSURLConnection downloads to data (asynchronously or synchronously).

Here's what I used (in a thread) for CURLHandle:
    
// "self" is an NSURL, or could be [NSURL URLWithString:@"http://www.blah.com"] - whatever you want
CURLHandle *urlHandle = (CURLHandle *)[self URLHandleUsingCache:NO];
NSString *error;
NSData *downloadedData;

[urlHandle setURL:self];
[urlHandle prepareAndPerformCurl];
error = [urlHandle curlError];

if (error && [error length]>0) {
	return [@"Error! " stringByAppendingString:error];
} else {
	downloadedData = [urlHandle resourceData];
	return [downloadedData autorelease];
}


Now this code works pretty well, however somehow the NSData gets messed up. For example, I would use this code to download some XML and load it using CFXMLTreeCreateFromData but many times that would return nil and I had no idea why (NSData wasn't nil).

Anyways, if you want to be a n00b and a quick solution for downloading you could use this:
    
NSData *data = [[NSData alloc] initWithContentsOfURL:[NSURL URLWithString:@"http://www.blah.com"]];
return [data autorelease];


But finally, after struggling with my code's errors (not syntax), I found the solution. The way I use this is in a thread and it loads synchronously, although you can have it load asynchronously. This method is the fastest by far. I'd say 2x-3x faster then CURLHandle, and the data worked 100% with the CoreFoundation XML classes.
    
// once again "self" is an NSURL
NSError *error;
NSURLResponse *response;
NSURLRequest *request = [NSURLRequest requestWithURL:self
       cachePolicy:NSURLRequestReloadIgnoringCacheData timeoutInterval:5];
NSData *data = [NSURLConnection sendSynchronousRequest:request
         returningResponse:&response error:&error];

return data;


Enjoy! --KevinWojniak

----

If you use CURLHandle in a thread (NSThread), my experience shows that setting the option CURLOPT_NOSIGNAL helps a lot (my code is more stable, and doesnt crash).

    
/*
      From the documentation for libcurl:
      CURLOPT_NOSIGNAL
           Pass a long. If it is non-zero, libcurl will not use any  functions
           that install signal handlers or any functions that cause signals to
           be sent to the process. This option is mainly here to allow  multi-
           threaded  unix  applications  to  still set/use all timeout options
           etc, without risking getting signals.  (Added in 7.10)
*/
         curl_easy_setopt([mURLHandle curl], CURLOPT_NOSIGNAL, 1);


StephanBurlot

