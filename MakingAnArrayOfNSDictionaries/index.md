---
layout: page
title: MakingAnArrayOfNSDictionaries
---


My goal is to get some protein name and sequence data into an NSTableView. The problem is getting that data into an array of dictionaries.
I would like to make a loop that simple creates a new dictionary for each increment of the counter, but can't figure out how to do it.
The issue isn't with the methods of NSDictionary or NSArrays, it is a more fundamental problem with automatically generating objects.
Can one use a variable to automate the naming of pointers as a loop progresses? dictionary0, dictionary1, dictionary2 etc.  Any help here would be appreciated. 

    
NSMutableDictionary *dictionary0 = [[NSMutableDictionary alloc] init];
[dictionary0 setObject: [namearray objectAtIndex: 0] forKey: @"name"];
[dictionary0 setObject: [proteinsequencearray objectAtIndex: 0] forKey: @"sequence"];

Thanks,
Tim



----

How about something like this for adding new dictionary's to an array.

    
int i; for (i = 0; i < [nameArray count]; i++)
{
     NSMutableDictionary *_intern = [[NSMutableDictionary alloc] init];
     [_intern setObject:[nameArray objectAtIndex:0] forKey:@"Name"];
     [_intern setObject:[proteinSequenceArray objectAtIndex:0] forKey:@"Sequence"];
     [overAllArray addObject:_intern];
     [_intern release];
}


or am I misunderstanding the problem? -- MatPeterson

----

No, that works. Sorry for the beginner question, and thank you for your help.

