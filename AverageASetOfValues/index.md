---
layout: page
title: AverageASetOfValues
---

A quick question about mathematics in Cocoa. I can't find a good answer to this. What I want to do is take a set of values, either in an NSArray, NSSet, or by themselves, and average them. Specifically, I have several NSTextFields which can be filled in with a number. They will be NSDecimalNumber values. The user will click "Calculate" and they will be averaged. Is there a good way to do this? Do I have to perform the mathematical operations or can I just take the average? Also, if some of the fields are blank, will the averaging take this into account, or will it see them as zero, and mess up the average? Much appreciated, --LoganCollins


----

Your table view data should be an array of dictionary objects, so all you would have to do is ask the table data array for the average of the column's key value. Say you want the average age of all the entries in a table and the column identifier is "age". Just ask the table data array for the average     [tableData valueForKeyPath:@"@avg.age"] Below is a little example of this KVC feature.

    

	objects = [NSMutableArray array];
	unsigned int i;
	for (i = 0; i < 256; i++) {
		NSMutableDictionary *entry = [NSMutableDictionary dictionary];
		[entry setValue:[NSNumber numberWithInt:i] forKey:@"index"];
		[objects addObject:entry];
	}
		
	NSNumber *average = [objects valueForKeyPath:@"@avg.index"];
	NSLog(@"average: %@", average);



--zootbobbalu

----

But how can adapt this to use the values from the NSTextField's that are connected? That works great otherwise. Thanks, --LoganCollins

----

Okay, maybe I'm completely off track. :) I'm not too versed in math operations in Cocoa yet. Let me just start further back. I have the several text fields that I want averaged into a "result" text field. How do I accomplish this? By the code above? --LoganCollins

----

Sorry, I didn't understand your question. I think the best thing to do in your case is to just get the values from each text field and then do the average by adding the values and dividing by the number of text fields. --zootbobbalu

----

That's what I was planning to do, however, I realized that if the user does not use all the fields the division will be off. It will divide by the total number of fields, not the total number being used. Any way you can think of to resolve that? --LoganCollins

*One possible solution would be to get the fields'     objectValues as well as their     intValues (or     floatValues or     doubleValues). That way, the object value will be     nil when the field is empty. --JediKnil*

----

Okay, probably a stupid question, but how do I go about doing the mathematical operations on the values? I have them all as NSDecimalNumbers, but what do I do to add them all together and then divide them? Is there a set of methods for this? --LoganCollins

*Why are you using NSDecimalNumbers? Are your numbers really that big that you need to use that class? Use NSNumber if you can, and then just tally up the total via intValue or whatever they contain, summing them up into a normal primitive variable (i.e. double) and then dividing by the number of numbers.*

Okay, I understand that I have to add then divide, but how can I take into account if the user does not take advantage of all of the fields? It will divide by the wrong number. I could run if-else statements, but there would be like a thousand of them.

----

You use code like this (pseudocode):

    
int totalFields = 0;
int totalValue = 0;

foreach(field in fields)
   if(field stringValue] length] > 0)
      totalFields++;
      totalValue += [field intValue];

if(totalFields == 0)
   error, no fields used
else
   average = totalValue / totalFields;


----

I've never used foreach before, how is it used? What does fields represent, an array?

*You need to go back, study C, and study basic Objective-C. Lot of this stuff you're posting about is simple things you can learn easily in a book. Average values is a very simple concept that shouldn't be too hard to grasp. To answer your question, foreach doesn't exist in any C language. It's pseudocode like he mentions. You would replace that with [[NSEnumerator or for loops.*

