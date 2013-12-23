---
layout: page
title: NsUrlNotLoadingPages
---

I'm writing an app that makes digests of Slashdot posts. To download a story from Slashdot, I'm using the following code:

    
- (IBAction)gather:(id)sender
{
	NSString *storyText;
	NSString *storyAddr;
	NSURL *storyUrl;

	storyAddr = [storyUrlField stringValue];
	
	NSLog(@"String: { %@ }", storyAddr);
	
	storyUrl  = [NSURL URLWithString:storyAddr];
	
	NSLog(@"Story URL: { %@ }", storyUrl);
	
	storyText = [NSString stringWithContentsOfURL:storyUrl];
	
	NSLog(@"Story data: { %@ }", storyText);
}


Addresses like http://www.slashdot.org work fine, but for a full story address, such as...
http://slashdot.org/comments.pl?sid=121818&threshold=-1&mode=nested&commentsort=0&op=Change

...dont work. The NSLog'ings of **storyAddr** and **storyUrl** look fine, but the NSLog'ing of **storyText** simply produces:

Story data: {���

Note the lack of closing brace also. Does anyone have any idea what I am doing wrong here?

----

Most likely the slashdot server is returning a compressed (gzipped) page. You'd have to gunzip it.

-john t

----

in which case, of course, you should be using dataWithContentsOfURL: - or, better yet, some lower-level way of accessing the page which tells you the Content-Type and Content-Transfer-Encoding. *--boredzo*


----

A buddy of mine and I (mostly him) took just took on this problem and I think we got a pretty good solution.  Here's the NSString category; just make an empty .h file for it and add it to your project and you don't even have to change your existing code:

    

@implementation NSString (GzipAwareString)

+ (NSString *)stringWithContentsOfURL:(NSURL *)url
{
    NSString    *string;
    
    string = [[NSString alloc] initWithData:self class] gunzipedDataFromData:[[[NSData dataWithContentsOfURL:url]] encoding:NSASCIIStringEncoding];
    
    return [string autorelease];        
}

+ (NSData *)gunzipedDataFromData:(NSData *)compressedData
{
    NSTask  *gunzip;
    NSPipe  *compressedDataPipe, *uncompressedDataPipe;
    
    // you probably want to verify that the data is indeed compressed here (otherwise return compressedData)
    
    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
       
    gunzip = [[NSTask alloc] init];
    
    compressedDataPipe = [NSPipe pipe];
    uncompressedDataPipe = [NSPipe pipe];
    
    [gunzip setLaunchPath:@"/usr/bin/gunzip"];
    [gunzip setArguments:[NSArray arrayWithObject:@"-f"]];
    
    [gunzip setStandardInput:compressedDataPipe];
    [gunzip setStandardOutput:uncompressedDataPipe];
    
    [gunzip launch];
    
    NSFileHandle *writerHandle = [compressedDataPipe fileHandleForWriting];
    [writerHandle writeData:compressedData];
    
    [writerHandle closeFile];
    
    int maxToRead = (1024 * 1024);
    
    NSFileHandle    *readerFileHandle = [uncompressedDataPipe fileHandleForReading];
    NSMutableData   *uncompressedData = [NSMutableData data];
    
    do {
        NSData  *dataToRead;
        
        dataToRead = [readerFileHandle availableData];
        
        if (dataToRead && [dataToRead length]) {
            [uncompressedData appendData:dataToRead];
        } else {
            break;
        }
        
    } while ([uncompressedData length] < maxToRead);
    
    [gunzip terminate];
    [gunzip release];
    
    NSData *newData = [[NSData alloc] initWithData:uncompressedData];
    [pool release];
    [newData autorelease];
    if (!newData || ![newData length]) newData = compressedData;
    return newData;
}


@end



-nsorscher

