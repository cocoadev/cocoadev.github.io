---
layout: page
title: NSTokenField
---

NSTokenField<nowiki/>s are like NSTextField<nowiki/>s that display 'tokens' as blue rounded rectangle items, similar to the way to Mail.app shows email addresses in the To: Field.

They are a new addition in Tiger.

----

A GUI design note: it can be difficult to tell if there is a space around or between tokens when they are in the middle of some text. It might make sense to not use tokens in such situation, or else to pick a font where it is easy to see spaces.

*or make "space" a token*

----

-objectValue can be used to get the final content of this field (an array) to store in your model. Your delegate methods can define any object to be the token object, such as an NSDictionary containing arbitrary data set by your delegate methods

----

You can bind the value of a NSTokenField to a NSArray which contains NSStrings while it can be bound to a single NSString. If you want the content of a NSTokenField to be represented as blue rounded rectangles with Cocoa Binding, you should use NSArray.

----

As a subclass of NSTextField in 10.4/Tiger, NSTokenField has built-in undo, handled by the field editor's NSUndoManager (actually a private subclass). If you want to observe this manager for tracking dirtying ("isEdited"): In a delegate of all NSTokenField or NSTextField instances, implement -control:textShouldBeginEditing and call [fieldEditor undoManager]. Different instances of NSUndoManager are used at different times, so you may want to retain it and notice when a different instance is used, so that you can remove and add observers at the right times for the right instances. 

----

Is there a simple way to give tokens different colors, depending on their representedObject? tokenField:styleForRepresentedObject allows only plain text or rounded blue ...

----
What is a "token" exactly"

----
Tokens are just strings. NSTokenField lets a delegate decide which strings to offer as possible completions as the user types text into the field. If the user chooses one of the completions, NSTokenField displays that string, or token, differently. Think of the address fields in Mail.app, where addresses from your address book are offered as completions. -CS

----
NSTokenField has a bit of a quirk vs normal NSTextField: after successfully tokenizing a string to its represented object, it does not update its value binding, nor does it send an action to its target.  To update the binding, you effectively have to press the Return key twice, which just seems quite unintuitive to me.

The following is a subclass of NSTokenField and NSTokenFieldCell to fix this behaviour.  Additionally, the NSTokenField delegate can implement a new method named -tokenFieldDidTokenizeString:.  If it does, this will be called on a successful tokenization.  Enjoy!  -AndrePang

----
The private NSTokenFieldCell method overridden here to catch tokenizing in the act is not available on Mac OS 10.6. Instead you need to override _tokenizeCharactersAdjacentToSelectionForTextView: terminatorsNeeded: and perform the same steps after checking the returned BOOL value. 

Also, the selector signatures in the respondsToSelector: checks and performSelector: calls in the cell subclass are missing the ending colons. I've added the code to get this working on 10.6 and have fixed the missing colons. ~Phil

    
//***********************************************************************************

// Thank you once again, class-dump!

@interface NSTokenFieldCell (UndocumentedMethods)

- (void)_string:(id)fp8 tokenizeIndex:(int)fp12 inTextStorage:(id)fp16;

@end

//***********************************************************************************

@implementation RWTokenField

+ (void) load
{
	[RWTokenField poseAsClass:[NSTokenField class]];
}

- (void) tokenFieldCellDidTokenizeString:(NSTokenFieldCell*)tokenFieldCell
{
	NSDictionary* valueBindingInformation = [self infoForBinding:@"value"];
	if(valueBindingInformation != nil)
	{
		id valueBindingObject = [valueBindingInformation objectForKey:NSObservedObjectKey];
		NSString* valueBindingKeyPath = [valueBindingInformation objectForKey:NSObservedKeyPathKey];
		
		[valueBindingObject setValue:[self objectValue] forKeyPath:valueBindingKeyPath];
	}
	
	[self sendAction:[self action] to:[self target]];

	if(self delegate] respondsToSelector:@selector(tokenFieldDidTokenizeString:)])
	{
		[[self delegate] performSelector:@selector(tokenFieldDidTokenizeString:) withObject:self];
	}
}

@end

//***********************************************************************************

@implementation [[RWTokenFieldCell

+ (void) load
{
	[RWTokenFieldCell poseAsClass:[NSTokenFieldCell class]];
}

- (void)_string:(id)fp8 tokenizeIndex:(int)fp12 inTextStorage:(id)fp16
{
	[super _string:fp8 tokenizeIndex:fp12 inTextStorage:fp16];

	if(!self controlView] respondsToSelector:@selector(tokenFieldCellDidTokenizeString:)]) return;
	
	[[self controlView] performSelector:@selector(tokenFieldCellDidTokenizeString:) withObject:self];
}

/* added to work on 10.6 */
- (BOOL)_tokenizeCharactersAdjacentToSelectionForTextView:(id)fp8 terminatorsNeeded:(BOOL)fp12
{
	BOOL success = [super _tokenizeCharactersAdjacentToSelectionForTextView:fp8 terminatorsNeeded:fp12];
	if ( success ) {
		
		if(![[self controlView] respondsToSelector:@selector(tokenFieldCellDidTokenizeString:)]) 
			return success;
	
		[[self controlView] performSelector:@selector(tokenFieldCellDidTokenizeString:) withObject:self];
	}
	
	return success;
}

- (void) setObjectValue:(id<[[NSCopying>)object
{
	[super setObjectValue:object];
	
	if(!self controlView] respondsToSelector:@selector(tokenFieldCellDidTokenizeString:)]) return;
	
	[[self controlView] performSelector:@selector(tokenFieldCellDidTokenizeString:) withObject:self];
}

@end

//***********************************************************************************

----
It's not a bug as such; it allows you to enter some text, tokenize it, then enter some more text and tokenize *that*. Think about Apple Mail's address-based token field (which I personally dislike, but not for this reason). You could probably get around this by making the binding continuous, but then you'd also probably get the updates between tokenizations, which wouldn't be very useful. --[[JediKnil



----

I have an NSTokenField which is bound in IB to an array controller, like so:

http://farm3.static.flickr.com/2043/2714299783_6dc5b3bdfc.jpg

This path, tags, is an attribute of the entity Cloud, like so:

http://farm4.static.flickr.com/3186/2715113950_a254a93d82.jpg

This approach WORKS just fine for storing and binding the cloud's tags to a tokenfield.  The problem is that I cannot duplicate this in code.  I create a new NSTokenField, and then  call:

    
[newTokenField bind:@"value" 
                   toObject:cloudManagedObject 
            withKeyPath:@"tags" 
                    options:[NSDictionary dictionaryWithObject:@"NSKeyedUnarchiveFromData" forKey:@"NSValueTransformerBindingOption"]];


Running it with no tags in the Cloud, then typing them into the tokenfield, gives the error: "Unacceptable type of value for attribute: property = "tags"; desired type = NSData; given type = NSCFArray; value = ( aTag )."

If I establish the binding only after the tags are in place, I get this error: "<NSTokenFieldCell: 0x1ead30>: Unknown object type assigned (<62706c69 73743030 d4010203 04050609 0a582476 65727369 6f6e5424 746f7059 24617263 68697665 7258246f 626a6563 74731200 0186a0d1 07085472 6f6f7480 015f100f 4e534b65 79656441 72636869 766572a4 0b0c1213 55246e75 6c6cd20d 0e0f1056 24636c61 73735a4e 532e6f62 6a656374 738003a1 11800255 74726963 6bd21415 16175824 636c6173 7365735a 24636c61 73736e61 6d65a217 18574e53 41727261 79584e53 4f626a65 63740811 1a1f2932 373a3f41 53585e63 6a757779 7b81868f 9a9da500 00000000 00010100 00000000 00001900 00000000 00000000 00000000 0000ae>).  Ignoring..."

To my understanding, this code accomplished exactly what IB does.  So why doesn't it work?  It seems like it will transform in one direction but not the other.  I've tried all sorts of variations and I just can't get it to go... thanks in advance!

----
My solution has been to use this binding:

    
[newTokenField bind:@"value"
               toObject:cloudManagedObject
        withKeyPath:@"tags" 
                options:nil];


and this glue code

    
- (void)setTags:(NSData *)newTags
{
	//NSLog(@"new tags %@", newTags);

	[self willChangeValueForKey:@"tags"];
	[super setPrimitiveValue:[NSKeyedArchiver archivedDataWithRootObject:newTags] forKey:@"tags"];
	[self didChangeValueForKey:@"tags"];
}

- (NSData *)tags
{
	//NSLog(@"tags %@", [self primitiveValueForKey:@"tags"]);

	if ( [self primitiveValueForKey:@"tags"] )
		return [NSKeyedUnarchiver unarchiveObjectWithData:[self primitiveValueForKey:@"tags"]];
	else
		return [self primitiveValueForKey:@"tags"];
}


the result is that my coded tokenfields work, but the IB binding is broken...  triage...

