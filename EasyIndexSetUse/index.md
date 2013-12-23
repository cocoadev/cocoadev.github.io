---
layout: page
title: EasyIndexSetUse
---

Hi all. The addition of [NSTable selectedRowIndexes] has frustrated many, particularly when trying to run through the set and *do something* with each index at a time. Well that time has come to an end.

Code: With comments.
    
//Lets create a mutable index set because we're going to have to edit the set as we loop through it
NSMutableIndexSet *mutableIndexSet = [[[NSMutableIndexSet alloc] init] autorelease];
//Add the indexes you would like to loop through, to the mutable index set
[mutableIndexSet addIndexes:[myTable selectedRowIndexes]];

//Start us off with a loop
int a;
for(a=0;a<[mutableIndexSet count];a++)
{
//Lets grab the first index of the set
int cIndex = [mutableIndexSet firstIndex];
//cIndex is the current index of the loop!!

//Do something wild and fantastic with your index, like accessing an object from an array! Woooo
NSString *itemFromArray = [myArray objectAtIndex:cIndex];

//Now lets remove the current index from the set, so that the next time this loop runs the first index is actually the next index in the set
[mutableIndexSet removeIndex:cIndex];

//But, you'll find that the original count of the index set will soon mismatch. So you must tell the loop to loop one less time each time an index is removed.
a = a-1;
}


Pretty cool huh?

Code: For those who wanna jump right in. (No comments)
cIndex is the object that represents the current index of the loop
    
NSMutableIndexSet *mutableIndexSet = [[[NSMutableIndexSet alloc] init] autorelease];
[mutableIndexSet addIndexes:[myTable selectedRowIndexes]];

int a;
for(a=0;a<[mutableIndexSet count];a++)
{
int cIndex = [mutableIndexSet firstIndex];
NSString *itemFromArray = [myArray objectAtIndex:cIndex];
[mutableIndexSet removeIndex:cIndex];
a = a-1;
}


I have created a new class, LKEasyIndex that you can use to create a new array from another array based on an index set. This seems to be the most wanted.

-- Louis Klaassen

----

Wouldn't it be easier to use NSArray's objectsAtIndexes: method?

