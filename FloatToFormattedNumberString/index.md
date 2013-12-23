---
layout: page
title: FloatToFormattedNumberString
---



NSStringFormatFlags discusses this in detail

I thought it would be easy to do this, but I have been grappling with this for two days. I have an NSArray of NSMutableDictionary objects that contain one NSNumber (init with float) object at key "cost", and I want to save them to a plain text file formatted in currency. I have my while loop, but I can't work out the code to format the numbers when I add them to an NSMutableString in code. I guess this is confusing me because I can not use an NSNumberFormatter because it's going to a NSMutableString and not a cell. I want to format it with 2 decimal places. At the moment I get 0.4695888494 and I want it to be 0.47 with a currency symbol.

     
NSMutableString *mutString = 
	[[NSMutableString alloc] initWithString:@"Add floats to this: "];
NSEnumerator *enumerator;
enumerator = [myObjectArray objectEnumerator];
NSMutableDictionary *myFloatDic = [[NSMutableDictionary alloc] init];
while (myFloatDic = [enumerator nextObject]) {
	// This is the 1 line of code I need
	// mutString + [myFloatDic valueForKey:@"Cost"] <-- (NSNumber float)
}

// After this, I'll save the mutString in a text file with a NSSavePanel
// I have the code for this.
 

Any pointers would be really helpful. I realize that this is an easy question, and I have tried to read up on formatting, but there are so many aspects to it that I can not work it out for "in code" formatting. Thanks - Sky

----

You can just use     [NSString stringWithFormat:]. Try this:
    

while (myFloatDic = [enumerator nextObject]) {
 float fValue = [myFloatDic valueForKey:@"Cost"];

//"%.2f" is a printf style format string, formats float to 2 decimal places.
 NSString *floatString = [NSString stringWithFormat:@"%.2f", fValue];

//outputs one value per line; add your currency symbol here.
 [mutString appendString:[floatString stringByAppendingString:@"\n"]]; 
}



Also, you can remove the assignment after your     myFloatDic declaration - it's not neccessary and causes a memory leak.
----
Thanks. It looks so easy to see it, but I didn't know that     %.2f was a float to 2 decimal places. I'm not used to command line stuff. I will read up on memory leaks, because I have not been taking it into account at all. My program must be like one of those water barrels that's been shot up in a western movie. <grin> -- Sky
----
    stringWithFormat: uses the same format strings as     printf, so you can find out what sorts of things you can do with it by typing     man 3 printf in Terminal.

----

You could also add the currency symbol and line break right in the format string - just change it to @"$ %.2f\n" or whatever

