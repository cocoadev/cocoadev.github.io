---
layout: page
title: FileExistsAtURL
---



I haven't found a method to check if a NSURL is valid.

Can anybody help me?

----

get the path from the url and check that

----

The file is on a webserver.....

----

Then you need to connect to the url and see if it comes back 404. See http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSHTTPURLResponse.html#//apple_ref/doc/uid/20001694/CJBECBFB

----

If it's an http URL, it would be normal to use an http 'HEAD' request to check this, because a HEAD request only retrieves the http headers from the server, not the content.  Something like this is what you'd expect to work:

    
@implementation NSURL (ValidityChecking)

- (BOOL)httpIsValid
        BOOL isValid = NO;
        NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:self];
        [request setHTTPMethod:@"HEAD"];
        NSHTTPURLResponse *response = nil;
        [NSURLConnection sendSynchronousRequest:request
                              returningResponse:&response
                                          error:NULL];
        if ((response != nil) && ([response statusCode] == 200))
                isValid = YES;

        return isValid;
}

@end


Unfortunately, this turns out to be very slow on Panther.  Cocoa sits around waiting for data that will never come. And since the NSHTTPURLProtocol class is private, there's no obvious way to extend it to be more sensible...

So I'd look at CURLHandle or something.  You need to do the equivalent of the above.

I don't think there's a way to do this in a protocol independent fashion.

----

You ought to be able to get around the slowness by using asynchronous methods. You get notified once the headers have arrived, and at that point you can cancel the connection.

----

Here is one reason it might be so slow:

http://www.cocoabuilder.com/archive/message/cocoa/2004/1/23/99402

Seems the default is Connect:keep-alive which you'd want to change to close, though that post also says he could not change it to close. Maybe this bug is fixed in Tiger?

I hope someone can help to get this working as I need to do HEAD requests myself and would prefer to not use CURLHandle. --MikeZornek

----

It's fixed in Tiger.  It isn't slow.

----

Some misbehaving web apps don't respond to a HEAD. So I modified this code to check for a response via HEAD, and if that doesn't work, check via GET. Here's an example of such a URL: http://thomas.loc.gov/cgi-bin/bdquery/z?d110:SN03335:@@@L&summ2=m&

