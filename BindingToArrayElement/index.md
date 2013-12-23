---
layout: page
title: BindingToArrayElement
---



Could someone explain to me how to use cocoa bindings to bind a set of NSTextFields to individual Elements of an NSArray? 
Ive tried a few ways with no luck.

Thanks

----

What do you mean by "a set of NSTextFields" ?  If it's just a nib with multiple text fields, no can do.  The NSTextField's value binding needs a NSString.

This is simple if you can bind against a NSDictionary instead.


----

Yeh, it was set of text fields in a nib, how would you do this with an NSDictionary? Which controller should I Use and what would be the method of binding to one object in the dictionary (keypath?).
Cheers for your help. 


----

Use the NSObjectController and bind to the NSTextField's value binding.  Yes, you'd use the keypath to differentiate between objects in the dictionary.

Also, check this out if you haven't already:
http://www.cocoadevcentral.com/articles/000080.php
----

Ok thanks done that, But i dont seam to be able to change the value programatically and get the nib to update.
I have tried the following:

[array1 willChangeValueForKey:key];

[self setValue:inum forKeyPath:keypath] //have also tried: [array1 setValue:inum forKey:key];

[array1 didChangeValueForKey:key];

I then have a For loop over NSLog to print out the values in the NSDictionary and they have changed correctly but the Nib will not update.
Have also just tried [will/didchangeValueForKey:keyPath], no luck.

----
Are you storing your data in an array or in a dictionary? The above seems inconsistent in that regard. setValue:forKey: does something completely different for arrays than it does for dictionaries. If you call setValue:forKey: on an array, the array iterates through all the objects it contains and calls setValue:forKey: on each one. Dictionaries, on the other hand, are made up of pairs of keys and values, so calling setValue:forKey: on a dictionary only changes one of the stored objects.

Is your container (array or dictionary) mutable? That'd be important, since you seem to want to change it.

What sort of controller do you have bound to the container? In other words, when you call willChangeValueForKey: and didChangeValueForKey:, you're telling any object that's observing that object that something is about to change/has just changed. But that doesn't do any good if nobody is observing the object, right? You'll want to have an NSObjectController or NSArrayController (depending on the container) set to use your container as its content. Then you'll bind your text fields to that controller.

When you say "[self setValue:inum forKeyPath:keypath]", what object is "self"?

There's much here that's unclear. Rather than keeping us guessing, perhaps you could explain clearly what it is that you're trying to accomplish and what you've got so far. I know it's hard to show exactly what's going on when you're using bindings, since there's little code, but do post the code that you do have. -CS

----

OK,
My data is in a NSMutableDcitionary.
I am using a NSObjectController instance in my Nib, linked to my custom subclass of NSObject I created to handle the interface (which is the "self" object). Controller key is selction and model keypath is: array1.cell1

array1 is what my dictionary is called and cell1 is an NSNumber object within it. 
This is the code from my custom object: (yes i know its full of memeory leaks and is rather clumsy)
#       

#import "BindingTestObj.h"

@implementation BindingTestObj
-(void)awakeFromNib
{
	array1 = [[NSMutableDictionary alloc] init];
	NSLog(@"Awake From Nib Called");
	NSNumber *itemstring;
	
	int i;
	for (i=0; i<=1; i++) {
		NSNumber *inum = [NSNumber numberWithInt:i];
		NSString *key = [self returnCellNameForIndex:inum];
		NSLog(@"itteration %@",[NSNumber numberWithInt:i]);
		itemstring = [NSNumber init];
		[array1 setObject:itemstring forKey:key];
		//[array1 setValue:inum forKey:key];
		NSString *keypath = @"array1.";
		[self willChangeValueForKey:[keypath stringByAppendingString:key]];
		[self setValue:inum forKeyPath:[keypath stringByAppendingString:key]];
		[self didChangeValueForKey:[keypath stringByAppendingString:key]];
		
		NSLog(@"item at keypath %@ is %@",key,[array1 objectForKey:key]);
	}
}

-(NSString*)returnCellNameForIndex:(NSNumber*)index
{
	NSNumber *inum = index;
	NSMutableString *cellName = [[NSMutableString alloc] initWithString:@"cell"];

	[cellName appendString:[inum stringValue]];
	
	return cellName;
}

-(void) state //called from Nib to reveal contents of dictionary
{
	int i;
	for (i=0; i<=1; i++) {
		
		NSNumber *inum = [NSNumber numberWithInt:i];
		NSString *key = [self returnCellNameForIndex:inum];
		NSLog(@"item at keypath %@ is %@",key,[array1 objectForKey:key]);
		
	}
	 
}
	
@end
# 

Thanks for trying to help. -DG

