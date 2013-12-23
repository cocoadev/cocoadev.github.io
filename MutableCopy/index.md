---
layout: page
title: MutableCopy
---



Here is my scenario. I have a management application that I decided to write as my first app using Cocoa. I have some experience with Java but am new to Objective-C. I have two questions that have been causing me some grief.

First my app is designed to have multiple Leagues, these leagues have weeks, and a week has a set number of players. I created a model object for a league, week, and player. Each one of these has a NSMutableArray that holds the child (lack of a better word.)

I have one masterclass that contains the array for the league, and is also the class that I save (document based app) so that the app saves all the data.

What i am trying to do with copy is when the user clicks on create new week the previous week array (the player information) is copied to the new week. I have this some what working however If i make a change to player in the array that player is changed for all the arrays. I thought mutablecopy is taking a copy of the entire array and whatever is in it and init'ing a new object. Here is the code that I have written for the creation of a new week:

    
-(void)insertObject:(Week *)w inWeeksAtIndex:(int)index
{
	int weekCount = [weeks count];
	if (weekCount != 17)
	{
		if (weekCount != 0)
		{
			[weeks insertObject:w atIndex:index];
			[w createPlayers];
			[w setPlayers:[weeks objectAtIndex:(weekCount - 1)] players] mutableCopy;
			[w setWeekName:[NSString stringWithFormat:@"Week %d", (weekCount + 1)]];
		} else {
			[weeks insertObject:w atIndex:index];
			[w createPlayers];
			[w setWeekName:[NSString stringWithFormat:@"Week %d", (weekCount + 1)]];
		}
	}
}


This creates the new week and when I do an insertobject into this array the behavior is as expected. However the behavior is not as expected when I make a change to an existing player object.

My second question I think I can solve based on the first. I want to limit the number of players that are added to the league based on a instance variable defined in the league class (seperate class from the week class). I have tried several ways to do this but nothing compiles correctly. i normally receive an error in the log saying that the league is not responding to the KVC of numberOfPlayers,

Everything is based on the selected index of various tables as an aside (leagues, are in a table, weeks are in a seperate table, and players are displayed in their own table).

Thanks for any help that you can give.

----

Copies in Cocoa are shallow copies, not deep copies. This means that when you do     [array copy] or     [array mutableCopy], you get an array which is separate from the original array, but which still points to all the same objects. If you want to perform a deeper copy, you'll have to do it manually, for example by iterating through the original array, making copies of the contents, and adding those copies to a new array.

----

An easier way would be to alloc a new array, and use the "initWithArray:(NSArray *)array copyItems:(BOOL)flag" initializer. Remember that you'll have to implement the NSCopying protocol on all your model classes that you're creating copies of.

----

Some code I'm using for deep copy (requires 10.2) that can be easily adapted for deep array copy:

    

@interface NSDictionary (MutableDeepCopy)

- (id) mutableDeepCopy;

@end

@implementation NSDictionary (MutableDeepCopy)

- (id) mutableDeepCopy
{
	NSData * tData;
	id tMutableDeepCopy=nil;
	NSString * tErrorString;
	
	tData=[NSPropertyListSerialization dataFromPropertyList:self
										         format:NSPropertyListBinaryFormat_v1_0
									   errorDescription:&tErrorString];
	
	if (tData!=nil)
	{
		NSPropertyListFormat tFormat;
	
		tMutableDeepCopy=[NSPropertyListSerialization propertyListFromData:tData
												       mutabilityOption:NSPropertyListMutableContainersAndLeaves
												                      format:&tFormat
												         errorDescription:&tErrorString];
	
		if (tMutableDeepCopy==nil)
		{
			[tErrorString release];
		}
		else
		{
			tMutableDeepCopy=[tMutableDeepCopy mutableCopy];
		}
	}
	else
	{
		[tErrorString release];
	}
	
	return tMutableDeepCopy;
}

@end


*This won't work if there are any non-property-list items in the array. What if I had an NSAttributedString in the array? Probably you would just have to do a recursive copy here...*

