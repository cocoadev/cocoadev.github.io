---
layout: page
title: CURLHandle
---

CURLHandle [http://curlhandle.sourceforge.net/] is a wrapper around a CURL.

Curl [http://curl.haxx.se/] is a command line tool for transferring files with URL syntax, supporting FTP, FTPS, HTTP, HTTPS, GOPHER, TELNET, DICT, FILE and LDAP.  Curl supports HTTPS certificates, HTTP POST, HTTP PUT, FTP uploading,  kerberos, HTTP form based upload, proxies, cookies, user+password  authentication, file transfer resume, http proxy tunneling and a busload of other useful tricks.

----

Some example code would be really beneficial to some new Cocoa developers.  I know how to use curl just fine, but can't get this to work within my code.  

----

Here is my class.  I'm not sure if I am always retaining/releasing correctly, but it seems to work.
    

@class CURLHandle;

@interface MEWebFetcher : NSObject <NSURLHandleClient>
{
	CURLHandle *mURLHandle;
}

+ (MEWebFetcher *)sharedInstance;

- (NSString *)fetchURLtoString:(NSURL *)url;
- (NSString *)fetchURLtoString:(NSURL *)url withTimeout:(int)secs;
- (NSData *)fetchURLtoData:(NSURL *)url;
- (NSData *)fetchURLtoData:(NSURL *)url withTimeout:(int)secs;

@end

#define DEFAULT_TIMEOUT 60

@implementation MEWebFetcher

#pragma mark Housekeeping Methods

- (id)init
{
    self = [super init];
	[CURLHandle curlHelloSignature:@"XxXx" acceptAll:YES];	// to get CURLHandle registered for handling URLs
	return self;
}

- (void)dealloc
{
	[CURLHandle curlGoodbye];	// to clean up
}

+ (MEWebFetcher *)sharedInstance
{
	static MEWebFetcher *sharedInstance = nil;
	if (!sharedInstance)
		sharedInstance = self alloc] init];
	return sharedInstance;
}

#pragma mark -
/* @parameters:
				url  is a valid NSURL
   @result:
				returns the result of calling fetchURLtoString:withTimeout: passing DEFAULT_TIMEOUT				
*/
- ([[NSString *)fetchURLtoString:(NSURL *)url 
{
	return [self fetchURLtoString:url withTimeout:DEFAULT_TIMEOUT];
}

/* @parameters:
				url   is a valid NSURL
				secs  is the number of seconds before the request for url will be given up.
   @result:
				returns the data associated with the url.  Returns nil if there was an error.				
*/
- (NSString *)fetchURLtoString:(NSURL *)url withTimeout:(int)secs
{
	NSData *urlData     = self fetchURLtoData:url withTimeout:secs] retain];
	[[NSString *urlString = [[NSString alloc] initWithData:urlData encoding:NSASCIIStringEncoding];
	
	//[urlData release]; // JRC - I still don't understand release I guess...
	//NSLog(@"urlData count: %i",[urlData retainCount]);
	return [urlString autorelease];
}

#pragma mark -
/* @parameters:
				url  is a valid NSURL
   @result:
				returns the result of calling fetchURLtoData:withTimeout: passing DEFAULT_TIMEOUT				
*/
- (NSData *)fetchURLtoData:(NSURL *)url
{
	return [self fetchURLtoData:url withTimeout:DEFAULT_TIMEOUT];
}

/* @parameters:
				url   is a valid NSURL
				secs  is the number of seconds before the request for url will be given up.
   @result:
				returns the data associated with the url.  Returns nil if there was an error.				
*/
- (NSData *)fetchURLtoData:(NSURL *)url withTimeout:(int)secs 
{
	NSData *data; // data from the website
	mURLHandle = [(CURLHandle *)[url URLHandleUsingCache:NO] retain];
	
	[mURLHandle setFailsOnError:NO];		// don't fail on >= 300 code; I want to see real results.
	[mURLHandle setUserAgent: @"Mozilla/4.5 (compatible; OmniWeb/4.0.5; Mac_PowerPC)"];
	[mURLHandle setConnectionTimeout:secs];
	
	data = mURLHandle resourceData] retain];
	if ([[NSURLHandleLoadFailed == [mURLHandle status])
	{
		NSLog([mURLHandle failureReason]);
		return nil;
	}
	
	[mURLHandle release];
	return [data autorelease];
}

- (void)URLHandle:(NSURLHandle *)sender resourceDataDidBecomeAvailable:(NSData *)newBytes
{

}

- (void)URLHandleResourceDidBeginLoading:(NSURLHandle *)sender
{

}

- (void)URLHandleResourceDidFinishLoading:(NSURLHandle *)sender
{

}

- (void)URLHandleResourceDidCancelLoading:(NSURLHandle *)sender
{

}

- (void)URLHandle:(NSURLHandle *)sender resourceDidFailLoadingWithReason:(NSString *)reason
{

}
@end



These URLHandle... methods are necessary as far as I can tell.  I hope this helps... does anyone know of a more minimalistic implementation?

-Joe

----

Since I've posted this example code, I have found some bugs. Namely: 
    
[CURLHandle curlHelloSignature:@"XxXx" acceptAll:YES];	// to get CURLHandle registered for handling URLs


should be in awakeFromNib: or init: or a similiar method of the NSApplication delegate. Likewise:

    
	[CURLHandle curlGoodbye];	// to clean up


should be placed inside applicationWillTerminate: of the NSApplication delegate.

-Joe

----

There's a bundle of helpful compilation information on the POSTMethodANDNSURLRequest page. Someone might consider moving it here. :-)

----

Has anyone successfully built a universal CURLHandle with support for 10.2.8?  Thanks -- Joe

