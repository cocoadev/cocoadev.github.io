---
layout: page
title: FScriptShellScripts
---

ShebangAble has the code... but I'll post it again here... if you write an FScript and put it in a file starting with a shebang (#!) line pointing to this code, and then put that script in ~/Library/Scripts, it'll appear in that AppleScript menu on your menu bar and be executable from there... 
(UPDATE: **IT DOESN'T WORK YET**)
    
#import <Foundation/Foundation.h>
#import <FScript/FScript.h>
#import <stdlib.h>

int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

	//load script
	NSString* scriptPath = [NSString stringWithCString:getenv("_")];
	if(!cPath) cPath = argv[1]; // when called from the scripts menu we can get the path here...
	if(!scriptPath) {
		NSLog(@"This program is intended to be used by a shebang program only...for now");
		return 1;
	}
	NSString* scriptText = [NSString stringWithContentsOfFile:scriptPath];

	//Remove shebang
	NSScanner* shebangStripper = [NSScanner scannerWithString:scriptText];
	[shebangStripper scanUpToString:@"\n" intoString:nil];
	NSRange scriptRange;
	scriptRange.location = [shebangStripper scanLocation];
	scriptRange.length = [scriptText length] - scriptRange.location;
	scriptText = [scriptText substringWithRange:scriptRange];

	//Make a block
	scriptText = [NSString stringWithFormat:@"[%@]", scriptText];
	
	//execute
	id scriptResult = scriptText asBlock] value];
	
	//format result nicely and print
	scriptResult = [[[NSPropertyListSerialization dataFromPropertyList:scriptResult format:NSPropertyListXMLFormat_v1_0 errorDescription:nil];
	scriptResult = [[NSString alloc] initWithData:scriptResult encoding:NSUTF8StringEncoding];	
	printf("%s", [scriptResult cString]);
	
	
    [pool release];
    return 0;
}


