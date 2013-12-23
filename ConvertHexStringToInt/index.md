---
layout: page
title: ConvertHexStringToInt
---




The NSString method - intValue unfortunately returns 0 (zero) for a hex string such as @"0x1234"

So to convert a hex string to an int I had to revert to using strtol() in stdlib.h (you don't need to include the header file in your Cocoa source .m file)

http://developer.apple.com/documentation/Darwin/Reference/ManPages/man3/strtol.3.html

    

NSString			*theHexString = @"0x34fd";
unsigned short	theHexValue = (unsigned short)strtol([theHexString cStringUsingEncoding:NSMacOSRomanStringEncoding], nil, 0x10);

NSLog( [NSString stringWithFormat:@"Hex Value = 0x%4.4x\n", theHexValue] );



Regards :)

- DerekBolli

