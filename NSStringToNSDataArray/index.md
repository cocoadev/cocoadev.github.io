---
layout: page
title: NSStringToNSDataArray
---

 
 
What would be an easy way to take each character out of an NSString, and place it into an array of NSData's?  Or hell, to further that, what would be an easy way to go back from single NSData's into an NSString?  (for the latter, I would assume use a Mutable String, yes?)
 
Could someone provide some sample code for both, please?  Thanks :)

----
I'm sick and just got out of bed, so caveat emptor.

    

-(NSArray *)dataArrayFromString:(NSString *)theString
{
    NSMutableArray *myArray = [NSMutableArray arrayWithCapacity:0];

    int i;

    for (i=0; i < [theString length]; i++)
      {
	[myArray addObject: [NSArchiver archivedDataWithRootObject:[theString substringWithRange: NSMakeRange(i,1)]]];
      }
    return myArray copy] autorelease];
}


-([[NSString *)stringByAppendingDataInArray:(NSArray *)theDataArray;
{
    NSMutableString *myString = [NSMutableString stringWithCapacity:0];
    NSData *eachObject;
    NSEnumerator *arrayEnumerator = [theDataArray objectEnumerator];

    while (eachObject = [arrayEnumerator nextObject])
      {
	NSString *aCharacter = [NSUnarchiver unarchiveObjectWithData: eachObject];

	[myString appendString: aCharacter];
      }
    return myString copy] autorelease];
}



----

Using a for loop to go through the array will be faster than using [[NSEnumerator

*Can you quantify that? NSEnumerator is fast.*

----

It seems to be 'accepted knowledge' that there's a slight overhead with NSEnumerator. I doubt you'd see any noticable difference until you get up to an array with many thousands of elements though.

----

Is it just me, or do the above two code samples leak the initially allocated NSMutableArray and NSMutableString instances? Each of them is copy'ed and the copy is autoreleased, but the initial instance that operates as the scratch space is ignored. I'm still getting my head around retain and release, i come from java ;-)

*It appears fine to me. Any of the arrayWith... or stringWith... methods are autoreleased so you don't have to do anything with them unless you want to keep them around longer.*

----

The above code can be sped up by saying

    
    NSMutableArray *myArray = [NSMutableArray arrayWithCapacity:[theString length]];


and

    
    NSMutableString *myString = [NSMutableString stringWithCapacity:[theDataArray count]];


to roughly preallocate the amount of space that will be needed, instead of just passing zero for the capacity. Adding to mutable objects is much faster if the object already has enough room to do it in.

