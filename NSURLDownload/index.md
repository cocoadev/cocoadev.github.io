---
layout: page
title: NSURLDownload
---

I'm reading this, http://developer.apple.com/documentation/Cocoa/Conceptual/URLLoadingSystem/Tasks/UsingNSURLDownload.html

 and wonder what this is
    
[self setDownloadResponse:response];


I can't find the method for this anywhere; shall I create it myself?

----

From the comment on the line before, it looks like it just sets an ivar. Make sure you report this as a documentation bug. The method should be published with the sample code.

----

I looked through some Software Update-like code I implemented in one of my apps and saw that I have that method. I am not really sure if I found the code somewhere or if I just read somewhere what the method is supposed to do, but here it is. (downloadResponse is just a NSURLResponse ivar)

    - (void)setDownloadResponse:(NSURLResponse *)newDownloadResponse
{
	if(downloadResponse != newDownloadResponse)
	{
		[downloadResponse release];
		downloadResponse = newDownloadResponse;
	}
}

--TylerStromberg

----

Don't forget to retain that newDownloadResponse in the assignment.

    		downloadResponse = [newDownloadResponse retain];

----

**Instantiate an NSURLResponse**

Below is the only way I could find to declare and instantiate an NSURLResponse **resp; as is needed by the NSURLConnection sendSynchronousRequest: returningReponse: error:  class method.

Surely there's a better way? NSURLResponse **resp = &[[NSURLResponse alloc] init]; wasn't going to work. :-/

    
	NSURLResponse *tempResp = [[NSURLResponse alloc] init];
	NSURLResponse **resp = &tempResp;
	NSError *tempErr = [[NSError alloc] init];
	NSError **err = &tempErr;		// this is ghastly... surely there is a syntax for this somewhere?
	NSData *bdsData = [NSURLConnection sendSynchronousRequest:request returningResponse:resp error:err];


----

Have you tried this?

    
	NSURLResponse *resp = nil;
	NSError *err = nil;
	NSData *bdsData = [NSURLConnection sendSynchronousRequest: request returningResponse: &resp error: &err];


You'll have to release the     resp and     err instances when you're done. -- SimonAndreasMenke

---- 
The response is a return value; you don't need to create an object yourself.  The above is correct in that sense.

The above is not correct in that it is not your responsibility to release     resp or     err.  To do so would be a bug.  The *only* methods that assign ownership of a returned object to the caller are those starting with alloc, copy, retain, and new (and init may replace one object owned by caller with a different object owned by caller).

----

Thanks both of you for your input. I've happily created them in my code so I can release them myself afterwards. I'm trying to avoid use of autorelease, particularly in the default pool, which is what I assume sendSynchronousRequest would do if I gave it nil response and error objects.

----

It's impossible to avoid autoreleased objects here, and the objects you pass in make absolutely no difference.  In fact, you'll just leak them if you're doing it as you describe.

The response and the error are *return values*.
**They are not created by you, they cannot be created by you.**  If you have something in the pointer before calling the method, it won't fill in the object or anything like that, it'll just replace it (leaking the object if it wasn't autoreleased).  Your code should look precisely like that suggested to you, anything else is not useful.
 
Why are you trying to avoid autorelease, anyway?

*If you make your own NSAutoreleasePool around the code, you can avoid the default pool. Profile your app first, though. Chances are, you're just wasting your time.*

**Especially since the objects created and autoreleased internally to the framework will usually dwarf your own.**

*A handmade NSAutoreleasePool will catch those, too. Only profiling will tell what's useful, and where.*

