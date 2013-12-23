---
layout: page
title: WSMethodInvocationRefAndSOAPActionHTTPHeader
---

I've discovered how to make SOAP calls with the General/WSMethodInvocationRef and related functions, but I cannot make a General/SOAPAction appear in the outgoing HTTP header. Does anyone know how to do this?

It seems like it ought to be     General/WSMethodInvocationSetProperty( 
    soapCall, 
    kWSSOAPMethodSOAPAction , 
    (General/CFTypeRef @"http://ws.cdyne.com/General/AdvancedCheckAddress" );

but there is no such constant as kWSSOAPMethodSOAPAction.

-Rob

----

You can create a kWSHTTPExtraHeaders property for the invocation which is a General/CFDictionary of { key (General/CFString), val (General/CFString) } pairs. See:
http://developer.apple.com/documentation/Networking/Conceptual/General/WebServices/3_ref_folder/chapter_3_section_33.html
for details. The snippet below should be enough to get you going.
    
General/NSDictionary *headers = General/[NSDictionary dictionaryWithObject:@"urn:General/GoogleSearchAction" forKey:@"General/SOAPAction"];
/* your code to create the invocation and set its parameters - see General/WSMethodInvocationRef */
General/WSMethodInvocationSetProperty (rpcCall, (General/CFStringRef) kWSHTTPExtraHeaders, (General/CFTypeRef) headers);


The headers dictionary could also contain @"General/MyFunkyApp 1.0 (Macintosh; Mac OS X)" with key @"User-Agent" if you want fame in server logs :)

-- Merman

----

I still have troubles getting soap to work, keep getting exception from my call.
It seem's that something is missing but i had several sleepless nights because of this anyone please help, i would be very gratefull.

--JJ

    

#import <General/CoreServices/General/CoreServices.h>
#import "soap.h"


@implementation soap


- (General/IBAction) getQuote: (id) sender {	
																																											
	
	General/WSMethodInvocationRef soapCall;
	General/NSMutableDictionary *params;
	params     = General/[[[NSMutableDictionary alloc] init] retain];



	[params setObject:@"AAPL" forKey:@"symbol"];
	
		
	General/NSMutableDictionary *headers = General/[NSMutableDictionary dictionaryWithObject:@"http://www.webserviceX.NET/General/GetQuote" forKey:@"General/SOAPAction"];	
	NSURL *rpcURL = [NSURL General/URLWithString: @"http://www.webservicex.net/stockquote.asmx"];	
	General/NSString *methodName = @"General/GetQuote";	
	
	General/NSDictionary *result;
	

	soapCall = General/WSMethodInvocationCreate ((General/CFURLRef) rpcURL, (General/CFStringRef) methodName, kWSSOAP2001Protocol);
	
	General/WSMethodInvocationSetParameters (soapCall, (General/CFDictionaryRef) params,(General/CFArrayRef) NULL);
	
	General/WSMethodInvocationSetProperty (soapCall, (General/CFStringRef) kWSHTTPExtraHeaders, (General/CFTypeRef) headers);
	result = (General/NSDictionary *) (General/WSMethodInvocationInvoke (soapCall));
		

if (General/WSMethodResultIsFault ((General/CFDictionaryRef) result)) 
	
	General/NSLog (@"%@", result);//[resultField setStringValue: [result objectForKey: (General/NSString *) kWSFaultString]];	


else 
	General/NSLog (@"%@", result);	
	[resultField setStringValue: [result objectForKey: (General/NSDictionary *) kWSMethodInvocationResult]];	
	
	[params release];
	params = nil;
	} 



@end



----
These examples are great and I've been able to use these as a model to call my .NET web services, so thanks to everyone for contributing. I need to take things a little further, now.

So far all the examples are sending parameters objects as strings. Can someone post a sample that sends an array of strings (or even better a structure of some sort that includes an array of strings) as one of the parameters? I've tried a few things but I'm failing miserably.

Thanks, 
PH
