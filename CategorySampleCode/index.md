---
layout: page
title: CategorySampleCode
---

**Implementing ClassCategories**

This sample code comes from a mailing list post by Uli Zappe and shows how to implement ClassCategories:


**MySillyStringAddition.h**
----
    
 #import <Foundation/NSString.h>
 
 @interface NSString (MySillyStringAddition)
 
 - (NSString *)sillyMethodThatReturnHello;
 
 @end



**MySillyStringAddition.m**
----
    
 #import "MySillyStringAddition.h"
 
 @implementation NSString (MySillyStringAddition)
 
 - (NSString *)sillyMethodThatReturnHello
 	{
 		return @"Hello";
 	}
 
 @end


----

See also KissyFaceView for an example of hiding private API using ClassCategories.  You may also want to see CocoaDevUsersAdditions for more keen categories.
----
I'm not sure of the value of this example but it was meant to serve as an example of what could usefully? be done, it's based on some SmallTalk code I once saw.

**URLString.h**

Add a Category so that a NSString can return the contents of a Web page as NSString.

    
 #import <Foundation/NSString.h>
 
 @interface NSString (asURL)
 - (NSString *)asURL;
 @end

 
**URLString.m**
    
 #import <Foundation/Foundation.h>
 #import "URLString.h"
 @implementation NSString (asURL)
 - (NSString *)asURL
 {
   NSURL *url = [NSURL URLWithString: self ];
   NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL: url
                                                          cachePolicy: NSURLRequestReloadIgnoringCacheData
                                                      timeoutInterval: 10];
   
   NSURLResponse *response;
   NSError *error;
   NSData *data = [NSURLConnection
                    sendSynchronousRequest: request
                    returningResponse: &response
                    error: &error];
   
   NSString *output = [NSString stringWithCString:[data bytes] length:[data length]];
   
   if (error) {
     NSLog(@"%@", error);
   }
   
   return [output autorelease];
 }
 @end

**main.m**

Example of silly use.

    
 #import <Foundation/Foundation.h>
 #import "URLString.h"
 int main (int argc, const char * argv[]) {
   NSAutoreleasePool * pool = NSAutoreleasePool alloc] init];
   
   NSString *page = [@"http://www.google.com" asURL];
   NSLog(@"Page = %@", page);
   
   [pool release];
   return 0;
 }

*Ahh, [[StringWithCString strikes again! Please don't use it, it is evil.*


Category:CocoaDevUsersAdditions

