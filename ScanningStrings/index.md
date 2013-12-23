---
layout: page
title: ScanningStrings
---

I am using and NSSCanner to scan out some quotes in a text view subclass, but the following code causes and endless loop and I cannot find the problem.

Can anyone help :-)?

    NSCharacterSet *charSet = [NSCharacterSet characterSetWithCharactersInString:@"\""];
NSScanner * theScanner = [NSScanner scannerWithString:[self string]];
[theScanner setCharactersToBeSkipped:[NSCharacterSet whitespaceCharacterSet]];	
	
BOOL inQuote = NO;
unsigned int quotePosition;
while ([theScanner isAtEnd] == NO) 
{
	if ([theScanner scanString:@"\"" intoString:NULL])
	{
			
		if (inQuote)
		{ 
			// We found a quote, except it's not working ;)
			NSLog([self substringWithRange:NSMakeRange(quotePosition,[theScanner scanLocation]-quotePosition)]);
		} else
		{
			inQuote = YES;
			quotePosition = [theScanner scanLocation];
		}
	}
[theScanner scanUpToCharactersFromSet:charSet intoString:nil];
}

----
Try adding     inQuote = NO; after your NSLog.
----
Thanks

