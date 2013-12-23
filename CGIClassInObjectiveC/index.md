---
layout: page
title: CGIClassInObjectiveC
---

I thought it would be interesting to write a CGI application in Objective C.   However, I was unable to find an Objective C class for handling CGI parameters so I wrote one myself.

You just create a Foundation tool and then work your way from the main function using the class.  For example, the following code will print out the name/value pairs passed to the CGI program:

    
#import <Foundation/Foundation.h>
#import "ObjectiveCGI.h"

int main (int argc, const char * argv[]) 
{
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

	// Create the CGI object
    ObjectiveCGI *cgi = [[ObjectiveCGI alloc] init];

    NSDictionary *params = [cgi getParams];
    NSEnumerator *enumerator = [params keyEnumerator];
    id key;

    printf("Content-type: text/plain\n\n");

    // Print out each key value pair as plain text
    while ((key = [enumerator nextObject])) 
    {
        printf("%s=%s",[key cString], 
                       params objectForKey: key] cString]);  
    }
        
    // Release the CGI object
    [cgi release];

    [pool release];
    return 0;
}


I've written some CGI programs, such as Vox Machina CGI (http://voxmachina.sytes.net) and [[VirtualSafari (http://virtualsafari.sytes.net) using this class.  And here's the code to the CGI class itself.  


    
#import "ObjectiveCGI.h"

@implementation ObjectiveCGI

- (id) init
{
	// dictionary for query key value pairs
	params = [[NSMutableDictionary alloc] initWithCapacity: 255];

	NSProcessInfo *processInfo = [NSProcessInfo processInfo];
	env = [processInfo environment];
	
	if (env objectForKey: @"REQUEST_METHOD"] isEqualToString: @"GET"])
		[self _parseGetQuery: [env objectForKey: @"QUERY_STRING";
	else if (env objectForKey: @"REQUEST_METHOD"] isEqualToString: @"POST"])
		[self _parsePostQuery];
	else
	{
		[[NSLog(@"Missing REQUEST_METHOD");
		return NULL;
	}
	
	if (self = [super init])
		return self;
}

- (void)dealloc
{
	[params release];
	[super dealloc];
}

- (NSString *)param: (NSString *)key
{
	return [params objectForKey: key];
}

- (NSDictionary *) getParams
{
	return params;
}

- (NSDictionary *) env
{
	return env;
}

- (void) _parseGetQuery: (NSString *)queryStr
{
	int i;

	if ([queryStr length] == 0)
		return;
	
	NSArray	*pairs = [queryStr componentsSeparatedByString: @"&"];
	
	for (i = 0; i < [pairs count]; i++)
	{
		NSArray *elem = pairs objectAtIndex: i] componentsSeparatedByString: @"="];
		[params setObject: [[elem objectAtIndex: 1] stringByReplacingPercentEscapesUsingEncoding: NSUTF8StringEncoding] forKey: [elem objectAtIndex: 0;
	}
}

- (void) _parsePostQuery
{
	int i;
	int len = env objectForKey: @"CONTENT_LENGTH"] intValue];
	if (len == 0)
		return;
	
	// read stdin for query string
	[[NSData *input = [[NSFileHandle fileHandleWithStandardInput] readDataOfLength: len];
	NSString *queryStr = [[NSString alloc] initWithData: input encoding: 1];
	NSArray	*pairs = [queryStr componentsSeparatedByString: @"&"];
	
	for (i = 0; i < [pairs count]; i++)
	{
		NSArray *elem = pairs objectAtIndex: i] componentsSeparatedByString: @"="];
		[params setObject: [[elem objectAtIndex: 1] stringByReplacingPercentEscapesUsingEncoding: NSUTF8StringEncoding] forKey: [elem objectAtIndex: 0;
	}
}

@end

