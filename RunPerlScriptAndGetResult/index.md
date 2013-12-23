---
layout: page
title: RunPerlScriptAndGetResult
---

Hey,

My application generates a temporary perl script and needs to run it from the command line.  Using NSTask is giving me incredible headaches - how would one go about running a perl script and returning the result that is located at, say, ~/myperlscript.pl? - THANKS.

This should catch anything that is spit out to STDOUT by the perl script:

    
NSTask *t = [[NSTask alloc] init];
NSPipe *p = [[NSPipe alloc] init];
NSFileHandle *h = [p fileHandleForReading];
NSString *path = [@"~/myperlscript.pl" stringByStandardizingPath];

[t setLaunchPath:@"/usr/bin/perl"];
[t setArguments:[NSArray arrayWithObjects:path, nil]];
[t setStandardOutput:p];
[t launch];
	
NSData *d = [h readDataToEndOfFile];
	
[t release];
[p release];
[h release];

NSString *stdout_contents = [[[NSString alloc] initWithData:d encoding:NSUTF8StringEncoding] autorelease];

