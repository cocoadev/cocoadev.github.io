---
layout: page
title: ListAllDeclaredTypeIdentifiers
---

Here is a quick tool that uses an undocumented API call to list all declared UniformTypeIdentifier<nowiki/>s. It could easily be rewritten to use CoreFoundation instead of Objective-C, but I'm too lazy to do it. Feel free to do it for me and post it here. --WAHa

    
#include <Foundation/Foundation.h>

NSArray *_UTCopyDeclaredTypeIdentifiers();

int main()
{
	NSAutoreleasePool *pool=[[NSAutoreleasePool alloc] init];
	NSArray *types=_UTCopyDeclaredTypeIdentifiers();
	int i;

	for(i=0;i<[types count];i++)
	{
		printf("%s\n",types objectAtIndex:i] UTF8String]);
	}

	return 0;
}


You build it with the following command line:
    
gcc -o lstypes lstypes.m -framework Foundation -framework Carbon


There is also a Perl script that uses this to [[ExportTypeIdentifiers.

