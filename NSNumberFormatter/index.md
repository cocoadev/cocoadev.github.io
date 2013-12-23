---
layout: page
title: NSNumberFormatter
---

Part of AppKit

Instances of NSNumberFormatter format the textual representation of cells that contain NSNumber objects and convert textual representations of numeric values into NSNumber objects. The representation encompasses integers, floats, and doubles; floats and doubles can be formatted to a specified decimal position. NSNumberFormatter objects can also impose ranges on the numeric values cells can accept.

----
This seems like it should be a simple thing to find, but I've searched around and I can't seem to find any information.

I have an NSTextField and I want to limit the text input to be numbers.  The only way I can see is to intercept each key event and delete the text if they enter anything other than a number (or similarly use a text changed event to do this).

Is there a nice clean way to do this like putting a filter on the text field to only accept characters from a certain set?  Or am I just having a pipe dream from prior programming work in Java?

----

Oh, I forgot that everything in Cocoa is done in the "cell" class (not the main class).  It turns out that I was looking for [NSCell setFormatter:].  Here's a nice document describing how to use data formatters:

http://developer.apple.com/documentation/Cocoa/Conceptual/DataFormatting/index.html

I kept searching for "NSTextField filter".  Using the right language when searching is half the battle.  Hopefully the keywords in this non-discussion will help someone else out. :)

----

I worked on this for the last week, but I cannot get this fixed. Google only knows about the problem, but nobody solved it yet. What I want is:

Subclass an NSNumberFormatter with:



* Formatterbehaviour 10_4 (10_0 has serious bugs with swiss franc)
* 0 should be formatted as $ 0.00 instead of 0 (default)
* It should also take unformatted values, by default it only takes the fully formatted value like '$ 12.38' and not '12.38'. I can get around this by converting the string into an NSDecimalNumber from the NSTextView shouldEndEditing:, but I want to do it inside the formatter.



I hope someone can help me out!

----

This might help with getting $ 0.00 instead of 0.  In a subclass of NSNumberFormatter, override the -stringForObjectValue: method to detect the case where the default formatter returns "0".  Substitute a properly formatted zero value instead.

    
- (NSString *)stringForObjectValue:(id)anObject;
{	
	NSString *string = [super stringForObjectValue:anObject];
	
	if ( [string isEqualToString:@"0"] ) 
	{
		// If we get back a plain "0", convert it to currency value.
		if ( [self numberStyle] == NSNumberFormatterCurrencyStyle ) {
			string = [NSString stringWithString:@"0.00"];
		}
	}

	return string;
}


This is a known bug in Tiger's NSNumberFormatterBehavior10_4, but apparently fixed in Leopard. See <http://lists.apple.com/archives/cocoa-dev/2007/Aug/msg00181.html > for the details.

-- BillGarrison

