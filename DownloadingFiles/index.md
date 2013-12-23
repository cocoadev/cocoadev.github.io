---
layout: page
title: DownloadingFiles
---

A really simple example for downloading files in Cocoa. (Thanks to Austin Shoemaker!)

    
NSURL *myURL = [NSURL URLWithString:@"http://www.apple.com/"];
NSData *urlContents = [myURL resourceDataUsingCache:YES];

if ([urlContents writeToFile:[@"~/Documents/applewebsite.html"
                 stringByExpandingTildeInPath]
                 atomically:YES])
{
	// It was successful, do stuff here
} else {
	// There was a problem writing the file
}


----
A slightly more complicated example that will download asynchronously or "in the background" by PeterMonty. I've tried to keep it as similar to the one above as possible for comparison.

    

// Starts download
- (void)download
{
	NSURL *myURL = [NSURL URLWithString:@"http://www.apple.com/"];
	[myURL loadResourceDataNotifyingClient:self usingCache:YES];
}

// This method will be called when the download has finished
- (void)URLResourceDidFinishLoading:(NSURL *)sender
{
	NSData *urlContents = [sender resourceDataUsingCache:YES];

	if ([urlContents writeToFile:[@"~/Documents/applewebsite.html"
                         stringByExpandingTildeInPath]
                         atomically:YES])
	{
		// It was successful, do stuff here
	} else {
		// There was a problem writing the file
	}
}

----

Is there anyway of putting a progress indicator to indicate how much it has downloaded?

Yes, read the docs, there is a method that will be called periodically. - SJI

----

Does anyone know how to cancel loadResourceDataNotifyingClient once it's started?  Alternatively, how about some instructions on using CURLHandle?

----

    I had the same problem loading an image - it kept being loaded from the cache. Using NSURL's resourceDataUsingCache solves the problem and is just as easy - in this case for an image but the URL can be for anything.

    
NSData *data = NSURL [[URLWithString:myURLString] resourceDataUsingCache:NO];
NSImage *image = [[NSImage alloc] initWithData:data];


- JoeZobkiw

----

For those with more advanced needs, CURLHandle by Dan Wood comes highly recommended ( http://curlhandle.sourceforge.net/ )

----

I highly recommend CURLHandle even for non-advanced needs. There are times when NSURL just won't do. For example, I've often found it has trouble downloading The Mac Observer and a few other sites.

---- 

I'm using code basically the same as the asynchronous example above. I also catch - URL: resourceDataDidBecomeAvailable: to display progress while the file is loading. But even though I have UsingCache:YES in both places, when I step over the line:

    
NSData *urlContents = [sender resourceDataUsingCache:YES];


The file is downloaded all over again... at least, I assume it is, because it takes ages to execute that one statement, during which time there is a lot of network activity. Has anyone come across that problem before?
-- AngelaBrett

----

Here's yet another snippet showing how to batch download files.

    

/* 

This code is licensed in the Public Domain. 
Please contribute any fixes back to hakan@konstochvanligasaker.se 
See http://konstochvanligasaker.se/code for det latest version. 

source code taken from:
http://konstochvanligasaker.se/code/src/BatchDownloader.h
http://konstochvanligasaker.se/code/src/BatchDownloader.m

patches:
- BOOL succeeded = [[NSFileManager defaultManager] createDirectoryAtPath:path attributes:nil];
+ BOOL succeeded = [[NSFileManager defaultManager] createDirectoryAtPath:path withIntermediateDirectories:NO attributes:nil error:nil];
(see also // ADDED code)

compile with:
gcc -Wall -framework Foundation -o batchdownload BatchDownloader.m

gcc -c BatchDownloader.m
nm BatchDownloader.o

test:
./batchdownload ~/Desktop/batchdownload http://www.google.com http://www.apple.com

*/
   

//#import <Cocoa/Cocoa.h>
#import <Foundation/Foundation.h>

@interface NSObject (BatchDownloaderDelegate)

// fired when one or more downloads are ready. The array contains a dictionary
// with URLs as the keys, and the local file paths as the keys.
- (void)downloadsReady:(NSDictionary *)downloads;

// array of URLs that could not be downloaded.
- (void)downloadsFailed:(NSArray *)downloads;

@end


@interface BatchDownloader : NSObject 
{
  // fast (constant-time) lookup for a URL, just given a download object. 
  // unfortunately we can't just use a dictionary with NSURLDownloads as keys, because it doesn't 
  // implement the NSCopying protocol.
  NSMutableDictionary *downloadPtrsToURLs;
  
  // URL => NSURLDownload 
  NSMutableDictionary *downloadURLsToObjects;
  
  // URL => local file path
  NSMutableDictionary *downloadURLsToLocalPaths;
  
  id delegate;
  
  NSString *destinationFolder;
}

// will try to create dest folder if it doesn't exist.
- (id)initWithDestinationFolder:(NSString *)path delegate:(id)delegate;

- (void)addDownload:(NSString *)URL;
- (void)addDownloads:(NSArray *)URLs;

@end


/* This code is licensed in the Public Domain. Please contribute any fixes back to hakan@konstochvanligasaker.se 
   See http://konstochvanligasaker.se/code for det latest version. */
   
//#import "BatchDownloader.h"

@interface NSObject (PtrValueUtils)
- (NSValue *)ptrValue;
@end

@implementation NSObject (PtrValueUtils)
- (NSValue *)ptrValue
{
  return [NSValue valueWithPointer:self];
}
@end

@interface BatchDownloader (Private)
- (void)removeAllTracesOfDownload:(NSURLDownload *)download URL:(NSString *)URL;
@end

@implementation BatchDownloader

- (id)init
{
  if ((self = [super init])) {
    downloadPtrsToURLs = [NSMutableDictionary new];
    downloadURLsToObjects = [NSMutableDictionary new];
    downloadURLsToLocalPaths = [NSMutableDictionary new];
  }
  return self;
}

- (id)initWithDestinationFolder:(NSString *)path delegate:(id)theDelegate
{
  if ((self = [self init])) {
    delegate = theDelegate;
    // does the dest dir exist?
    if (! [[NSFileManager defaultManager] contentsAtPath:path]) {
      // try to create it.
      //BOOL succeeded = [[NSFileManager defaultManager] createDirectoryAtPath:path attributes:nil];
      BOOL succeeded = [[NSFileManager defaultManager] createDirectoryAtPath:path withIntermediateDirectories:NO attributes:nil error:nil];

      // TODO: throw (or something) on failure
    }
    destinationFolder = [path copy];
  }
  return self;
}

- (void)dealloc
{
  delegate = nil;
  [destinationFolder release];
  
  [downloadURLsToObjects release];
  [downloadPtrsToURLs release];
  [downloadURLsToLocalPaths release];
  
  [super dealloc];
}

#pragma mark -

- (void)addDownload:(NSString *)URL
{

  NSURLDownload *download = [[[NSURLDownload alloc] initWithRequest:[NSURLRequest requestWithURL:[NSURL URLWithString:URL]] delegate:self] autorelease];
  
  NSString *destPath = [destinationFolder stringByAppendingPathComponent:[URL lastPathComponent]];

  //printf("URL: %s\n", [URL UTF8String]);
  //printf("destPath: %s\n", [destPath UTF8String]);

  [download setDestination:destPath allowOverwrite:NO];
  [downloadURLsToObjects setObject:download forKey:URL];
  [downloadPtrsToURLs setObject:URL forKey:[download ptrValue]];
}

- (void)addDownloads:(NSArray *)downloadPaths
{
  // TODO: start notification timer so we can report back in batches as well

  // start downloading
  NSString *URL = nil;
  NSEnumerator *downloadEnumerator = [downloadPaths objectEnumerator];
  while ((URL = [downloadEnumerator nextObject])) {
    [self addDownload:URL];
  }
}

- (void)removeAllTracesOfDownload:(NSURLDownload *)download URL:(NSString *)URL
{
  [downloadPtrsToURLs removeObjectForKey:[download ptrValue]];
  [downloadURLsToObjects removeObjectForKey:URL];
  [downloadURLsToLocalPaths removeObjectForKey:URL];

  // ADDED
  if([downloadPtrsToURLs count] == 0)
       CFRunLoopStop(CFRunLoopGetMain());
       //CFRunLoopStop(CFRunLoopGetCurrent());

}

#pragma mark -

- (void)download:(NSURLDownload *)download didCreateDestination:(NSString *)path
{
  [downloadURLsToLocalPaths setObject:path forKey:[downloadPtrsToURLs objectForKey:[download ptrValue]]];
}

- (void)download:(NSURLDownload *)download didFailWithError:(NSError *)error
{

  NSString *URL = [downloadPtrsToURLs objectForKey:[download ptrValue]];

  // notify delegate
  // TODO: batch these notifications
  if (delegate && [delegate respondsToSelector:@selector(downloadsFailed:)]) {
    [delegate downloadsFailed:[NSArray arrayWithObject:URL]];
  }
  
  [self removeAllTracesOfDownload:download URL:URL];
}

- (void)downloadDidFinish:(NSURLDownload *)download
{
  NSString *URL = [downloadPtrsToURLs objectForKey:[download ptrValue]];
  
  // notify delegate
  // TODO: batch these notifications
  if (delegate && [delegate respondsToSelector:@selector(downloadsReady:)]) {
    NSString *destPath = [downloadURLsToLocalPaths objectForKey:URL];
    [delegate downloadsReady:[NSDictionary dictionaryWithObject:destPath forKey:URL]];
  }
  
  [self removeAllTracesOfDownload:download URL:URL];
}

@end


// ADDED
int main(int argc, const char *argv[])
{

  if (argc < 3) return 1;

  NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];

  NSMutableArray *commandLineArguments = [NSMutableArray arrayWithArray:[[NSProcessInfo processInfo] arguments]];
  [commandLineArguments removeObjectAtIndex: 0];      // first object is the name of the executable

  NSString *destFolder = [commandLineArguments objectAtIndex: 0];
  [commandLineArguments removeObjectAtIndex: 0];

  BatchDownloader *urls =  [[BatchDownloader alloc] init];
  [urls initWithDestinationFolder:destFolder  delegate:urls ];
  [urls addDownloads: commandLineArguments];

  CFRunLoopRun();

  [urls release];

  [pool release];
   return 0;

}


