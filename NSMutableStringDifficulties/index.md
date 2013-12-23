---
layout: page
title: NSMutableStringDifficulties
---



I'm trying to create an RPN calculator program, and figured I'd handle the number entry by using an NSMutableString. Each new digit would be appended to the end of the string, and the first character would be either a blank for a positive number, or a dash for a negative number. 

The problem is that the program crashes after entering a second digit, or if I try to change sign twice (pos to neg to pos). 
Here are what I think are the relevant snippets of code:

    
	// here's how I initialize the NSMutableString *input
	input = [NSMutableString stringWithCapacity:256];
	[input setString:@" "];

	// Here's how I add a new digit to the string:
	[input appendFormat:@"%i", theTag];
	NSLog(@"Input string is '%@'", input);

	// Here's how the sign gets changed:
	char c = [input characterAtIndex:0];
	NSLog(@"The initial character is '%c'", c);
	if (c==' ') {
		NSLog(@"Going from positive to negative");
		[input replaceCharactersInRange:NSMakeRange(0,1) withString:@"-"];
	} else {
		NSLog(@"Going from negative to positive");
		[input replaceCharactersInRange:NSMakeRange(0,1) withString:@" "];
	}


The program dies with this message:

Executable "A03 RPN calculator" has exited due to signal 10 (SIGBUS)

That's the error indicating incorrect memory access, but I have no idea how I'm misusing the NSMutableString.  

Thanks for your help!

Norm Hecht

----
Hi! Maybe it�s because your mutable string dies at some point, try
    
NSMutableString * input;
input = [[NSMutableString alloc] initWithCapacity:256];
// �
// When you�re sure you don�t need it anymore, don�t forget:
[input release];

Hope this helps. Wdyp.

----

Wdyp is most likely correct, but opens what may be for you a huge can of worms. For additional insight, read the page SIGBUSError and then refresh your knowledge of ObjectCreation and MemoryManagement. You created your mutable string as an autoreleased object (just what that is has been copiously explained on many other pages.) Check out the AutoRelease page, too. Or MemoryLeak. QuickAutoreleaseQuestion will take you straight to the heart of what you need to consider.

An alternative to what Wdyp suggests is [ [ NSMutableString stringWithCapacity: 256 ] retain ] - and remember to release what you retain.
Understand how these two alternatives work and Bob's your uncle.

