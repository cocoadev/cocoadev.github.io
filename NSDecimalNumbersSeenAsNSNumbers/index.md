---
layout: page
title: NSDecimalNumbersSeenAsNSNumbers
---

I'm having trouble with NSDecimalNumbers that seem to magically turn into NSNumbers, which causes methods that
are intended for NSDecimalNumbers to come up "selector not recognized."

I have a bunch of NSDecimalNumbers that get stored to a file, and then read back in by a subsequent invocation
of my app.  All that seems to work fine; using the debugger, I "see" all the values I should see.  But then,
this method tries to operate on the read-in data:

    
-(void)MakeGroups:(NSMutableArray *)theTubeTable
	ofSize:(int) tubesInGroup 
	quantity:(int) numberOfGroups
{
	    NSDecimalNumber *dcpmhigh;
	    NSDecimalNumber *dcpmlow;
	    NSDecimalNumber *newdcpmlow;
	    NSDecimalNumber	*statlow;
	    double		statasdouble;

	    /* ... */

	    dcpmlow = theTubeTable objectAtIndex:currentIndex] objectForKey:@"Adjusted CPM"];

	    statasdouble = sqrt([dcpmlow doubleValue]);

	    statlow = [[[NSDecimalNumber decimalNumberWithString:[NSString stringWithFormat:@"%f", statasdouble]]; 

    	newdcpmlow = [dcpmlow decimalNumberBySubtracting:statlow];

	    /* ... */
}


...and for reference, "theTubeTable" above is build previously with the following method:

    
- (IBAction)setTubeTableFromFile:(NSString *)filePathToRead
{
    	[tubeTable removeAllObjects];
    	[tubeTable setArray:[NSArray arrayWithContentsOfFile:filePathToRead]];
}


When the last line in the MakeGroups method is run, I get:

2004-04-29 10:55:39.036 Wiper[6325] *** -[NSCFNumber decimalNumberBySubtracting:]: selector not recognized

I don't use an NSNumber explicitly anywhere; only NSDecimalNumbers.  This led me to believe that maybe I had 
not retained some data somewhere, and that I was being a victim of garbage collection--but as far as I can tell, 
I've retained everything I should need to retain...yet I still get the "selector not recognized."  The data file 
was created with only NSDecimalNumbers in it...so I don't know what gives.

Is there something about writing data to, or reading data from, a file, that would cause the data which was
once an NSDecimalNumber to be seen as an NSNumber instead?

I'd appreciate any help I can get troubleshooting selector-not-recognized thing.  I understand already that
"decimalNumberBySubtracting" isn't a method of NSNumber; it's a method of NSDecimalNumber...but, why isn't the
system thinking that variable "dcpmlow" is an NSDecimalNumber *, like I declared it?

Thanks in advance for any help troubleshooting...


Brian

----

The system is thinking     dcpmlow is a NSNumber because     theTubeTable objectAtIndex:currentIndex] objectForKey:@"Adjusted CPM"] is an [[NSNumber. It doesn't matter what you declared it as.

----

Okay, interesting.  However, the object at that index is an NSDecimalNumber.  At least, it was, when I added it to the array that was ultimately written to a file, and subsequently read back.  Is it possible that the process of writing or reading the file is changing my data from being seen as an NSDecimalNumber, to being seen as an NSNumber?  Thanks again...

Here's the snippet of code that originally assigns the value in question:

    
	cpm = [cpm decimalNumberByDividingBy:theExponential];
	
	[tubeTableEntry setObject:cpm forKey:@"Adjusted CPM"];


Brian

