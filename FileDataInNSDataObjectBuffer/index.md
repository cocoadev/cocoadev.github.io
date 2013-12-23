---
layout: page
title: FileDataInNSDataObjectBuffer
---



Hi All,

I'm developing a small class to process a data file which is a mixture of text & binary data. An example method for reading out the text encoded project version number is as follows, where buffer is an NSData object containing the entire file:

    
-(char *) projectVersionNr
{
	

	NSRange range = NSMakeRange(10, 4);
       [buffer getBytes: header range: range];	
	
	return header;
}


Rather than using a c char array to read out key portions of text I'd like to use the Cocoa framework. Can someone explain the way to read from the NSData object into a NSString object, with example code if possible. Or an alternative better solution altogether if I'm on the wrong track:-)

Thanks for your help.

----

Have you looked at the NSString documentation yet? In particular, these methods:

    
- (id)initWithCString:(const char *)nullTerminatedCString encoding:(NSStringEncoding)encoding
- (id)initWithData:(NSData *)data encoding:(NSStringEncoding)encoding


Take a look at the docs, just make sure to completely read through them, since a lot of the methods dealing with c-style strings have been deprecated recently.

