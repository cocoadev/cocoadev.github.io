---
layout: page
title: HowToScanForCharacterFollowedByInt
---


Hi!
Trying to find a way to scan a string with NSScanner. I've never used it before and need some help figuring out how to find an "s" followed by 2 numbers
i.e s75...

Heres my first lines of code for finding the "s" but it just locks the program...
Any ideas and suggestions is very much appreciated!

    
NSScanner *scanner = [NSScanner scannerWithString:fileName];
NSCharacterSet *charSet = [NSCharacterSet characterSetWithCharactersInString:@"s"];
NSString *scannerString;
			
while([scanner isAtEnd] == NO){
	[scanner scanUpToCharactersFromSet:charSet intoString:&scannerString];
}


----

infinity loop because there are no char before 's' in your example...

try with this code:
    
NSScanner *scanner = [NSScanner scannerWithString:fileName];
NSCharacterSet *charSet = [NSCharacterSet characterSetWithCharactersInString:@"75s"];
NSString *scannerString = nil;

while([scanner isAtEnd] == NO){
	if(![scanner scanUpToCharactersFromSet:charSet intoString:&scannerString]) break;
}

scannerString <- @"75"

----

Point of information: if you want to 'characterize' (no pun intended) a position in a string, use the term "digit", rather than "number".
A "number" can contain more than one "digit".

----
Ok, I  didn't mean to search the charSet for a "s"... 
I have a fileName, for example: @"gilmore.girls.s03e20" and I want to find the "03" after the "s" and "20" after the "e"... 
Maybe it's easier to just step through the whole string with -characterAtIndex??

----
Or just use regex, which is the most logical.

----
Try     /[a-zA-Z]([0-9]+)/ or the like.

----
Specifically,     s([0-9]+)e([0-9]+)$ should net you the season and episode number, assuming that there aren't any other extensions afterward. I'd suggest checking out the RegularExpressions software (q.v.).

