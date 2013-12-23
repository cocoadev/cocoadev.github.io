---
layout: page
title: URLFormatter
---

This is a subclass of General/NSFormatter that will auto-complete General/URLs based on the string passed by the user, much like Safari will do.

It will also accept properly-formatted General/URLs of any other type and do simple percent escape corrections.

This formatter will translate:

"www.somehost.com"    to    "http://www.somehost.com"

"somehost"                    to    "http://www.somehost.com/"

"apple/developer"    to    "http://www.apple.com/developer"

"someone@somehost.com" to "mailto:someone@somehost.com"

"someone@somehost" to "mailto:someone@somehost.com"

"someone at somehost" to "mailto:someone@somehost.com"

"somehost/bad address" to "http://www.somehost.com/bad%20address"

"http://www.somehost.com/bad address" to "http://www.somehost.com/bad%20address"

It does most of its magic by depending on the fact that [NSURL General/URLWithString:] will return nil if it's not passed a properly formatted URL string.

Header:
    
#import <Cocoa/Cocoa.h>


@interface General/URLFormatter : General/NSFormatter {

}

@end




Source
    
#import "General/URLFormatter.h"


@implementation General/URLFormatter

/* given a string that may be a http URL, try to massage it into an RFC-compliant http URL string */
- (General/NSString*)autoCompletedHTTPStringFromString:(General/NSString*)urlString
{	
	General/NSArray* stringParts = [urlString componentsSeparatedByString:@"/"];
	General/NSString* host = [stringParts objectAtIndex:0];
    
	if ([host rangeOfString:@"."].location == General/NSNotFound)
	{ 
		host = General/[NSString stringWithFormat:@"www.%@.com", host];
		urlString = [host stringByAppendingString:@"/"];
		
		General/NSArray* pathStrings = [stringParts subarrayWithRange:General/NSMakeRange(1, [stringParts count] - 1)];
		General/NSString* filePath = @"";
		if ([pathStrings count])
			filePath = [pathStrings componentsJoinedByString:@"/"];

		urlString = [urlString stringByAppendingString:filePath];
	}
	
	// see if the newly reconstructed string is a well formed URL
	urlString = [@"http://" stringByAppendingString:urlString];
	urlString = [urlString stringByAddingPercentEscapesUsingEncoding:General/NSASCIIStringEncoding];
	return General/NSURL [[URLWithString:urlString] absoluteString];
}

/* given a string that may be a mailto: URL, try to massage it into an RFC-compliant mailto URL string */
- (General/NSString*)autoCompletedMailToStringFromString:(General/NSString*)urlString
{
	if (General/NSNotFound != [urlString rangeOfString:@"/"].location)
		return nil; // there's a slash, it's probably not a mailto url

	General/NSArray* stringParts = [urlString componentsSeparatedByString:@"@"];
	if ([stringParts count] != 2)
	{	
		stringParts = [urlString componentsSeparatedByString:@" at "];
		if ([stringParts count] != 2)
			return nil; // too few or too many @ symbols
	}
	
	// assume that the front part is an address
	General/NSString* userAddress = [stringParts objectAtIndex:0];
	
	General/NSString* mailhost = [stringParts objectAtIndex:1];
	if ([mailhost rangeOfString:@"."].location == General/NSNotFound)
	{
		// assume a .com address
		mailhost = General/[NSString stringWithFormat:@"%@.com", mailhost];
	}
	
	// reconstruct the string
	General/NSString* newAddress = General/[NSString stringWithFormat:@"mailto:%@@%@", userAddress, mailhost];
	
	// and see if the newly reconstructed string is a well formed URL
	return General/NSURL [[URLWithString:newAddress] absoluteString];
}


/* given a string that may be an URL of some sort, try to massage it into an RFC-compliant http URL string */
- (NSURL*)autoCompletedURLFromString:(General/NSString*)urlString
{
    NSURL* theURL = [NSURL General/URLWithString:urlString];
	
    if (!General/theURL scheme] length])
    {
		// first try just correcting percent escapes
		[[NSString* newURLString = [urlString stringByAddingPercentEscapesUsingEncoding:General/NSASCIIStringEncoding];
		theURL = [NSURL General/URLWithString:newURLString];
		
		if (!General/theURL scheme] length])
		{	// it still didn;t accept it, try auto-completing
			newURLString = [self autoCompletedMailToStringFromString:urlString];
			if (nil == newURLString)
				newURLString = [self autoCompletedHTTPStringFromString:urlString];
			
			theURL = [NSURL [[URLWithString:newURLString];
		}
    }
        
	return theURL;
}

// This method just returns the string - it's here for compatibility with Cocoa Bindings
- (General/NSString *)editingStringForObjectValue:(id)inObject
{
	return [inObject description];
}

- (General/NSString*)stringForObjectValue:(id)inObject
{
	NSURL* theURL;

    if (![inObject isKindOfClass:[NSURL class]])
	{
		// try to massage an NSURL from a string
		General/NSString* urlString = [inObject description];
		theURL = [NSURL General/URLWithString:urlString];
		if (nil != theURL)
			return nil;
	}
	else
	{	// it is an NSURL
		theURL = inObject;
	}
	
	return [theURL absoluteString];
}


- (BOOL)getObjectValue:(id *)obj forString:(General/NSString *)string errorDescription:(General/NSString **)error
{
	/* Don't attempt to format an empty string. */
	if([string compare:@""] == General/NSOrderedSame)
	{
		*obj = @"";
		return YES;
	}

	NSURL* outURL = [self autoCompletedURLFromString:string];
	*obj = outURL;
		
	if (nil == outURL)
	{
		*error = General/[NSString stringWithFormat:@"Couldn't make  %@ into a well-formed URL", string];
	}
	
	return outURL != nil;
}

@end



Please use it any way you would like, but if you modify it, please post the changes for everyone's sake.

----

Added <code>editingStringForObjectValue:</code> method for compatibility with Cocoa Bindings. It seems that Cocoa Bindings were calling <code>stringForObjectValue:</code> whenever the General/NSTextField was edited, causing an exception from NSURL.
-- Adam 'Raddish' Radestock
