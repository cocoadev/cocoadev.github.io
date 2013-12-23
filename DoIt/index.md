---
layout: page
title: DoIt
---

Part of the DOSAStrategy... for info on DOSA see DOScriptingArchitecture

This is a command line interface to DistributedObjects for scripting and other fun.. note if the method returns something I dump it as an xml plist on stdout for now.  I considered the old ascii style might be easier to parse for some languages but most languages are getting better and better xml support, and many don't have good RegularExpression support...

If there was a object vended for "com.apple.iTunes" exposing a method like "-(void)playPause; // toggle play status" you would type:

>DoIt com.apple.iTunes playPause

and the method would be called... to pass strings, use FScript syntax

>DoIt com.someCompany.MyApplication myMethod:\'blah\'

without anything further... the code:

----

    
#import <Foundation/Foundation.h>
#import <FScript/FScript.h>

int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

	if(argc < 3) {
		NSLog(@"syntax: DoIt <bundleIdentifier> <command>");
		return 1;
	}
	
	id bundleId = [NSString stringWithCString:argv[1]];
	
	id doWhat = [NSString stringWithCString:argv[2]];	
	int index;
	const char* curArg;
	for(index = 3; index < argc; index++) {
		curArg = argv[index];
		doWhat =[NSString stringWithFormat:@"%@ %s", doWhat, curArg];
	}

	id fscriptCommand = [NSString stringWithFormat:@"[(NSConnection rootProxyForConnectionWithRegisteredName:'%@' host:nil) %@]",bundleId, doWhat];
	
	id result = fscriptCommand asBlock] value];

	result = [[[NSPropertyListSerialization dataFromPropertyList:result format:NSPropertyListXMLFormat_v1_0 errorDescription:nil];
	result = [[NSString alloc] initWithData:result encoding:NSUTF8StringEncoding];

	printf("%s", [result cString]);
	
    [pool release];
    return 0;
}


