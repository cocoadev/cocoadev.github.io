---
layout: page
title: ShebangAble
---

I want to make a program shebangable... that is, one should be able to write shell scripts that have the name of my program at the top of the file beside the shebang.  (actually not my program, but I want to make a way to write executable FScript-s... I will just make a small program that imbeds the FScript interpreter and does the FScript), Here's what I have discovered about making a program shebangable.

Here's a simple example scenario to explain what's going on.

the shebangable program is at /usr/local/bin/fscript with the following (not quite yet as nice as it could be) code:
    
#import <Foundation/Foundation.h>
#import <FScript/FScript.h>
#import <stdlib.h>
// stdlib for getenv...
int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

	//load script
	NSString* scriptPath = [NSString stringWithCString:getenv("_")];
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




the shebang script is at ./slashdot and contains the following
    
#!/usr/local/bin/fscript
NSWorkspace sharedWorkspace openURL:(NSURL URLWithString:'http://slashdot.org').


now when I write *./slashdot*, the program fscript gets the following argv: *["/usr/local/bin/fscript","./slashdot"]*... if there were arguments on the shebang line they get put in between the fscript and the slashdot section, and if the slashdot script got sent arguments they get added at the end.  To find the path of the file containing the script use *char* programPath = getenv("_");*  When reading it in, be sure to **skip the shebang line.** ;)

If anyone has any other things to say on this issue I'd like to hear...

*I'd just like to suggest that you switch to stringWithUTF8String: instead of stringWithCString: when converting paths to NSString. You'll blow up on paths that contain non-ASCII characters otherwise.*

