---
layout: page
title: NSTokenFieldOriginalValueFromRepresentedObject
---


Hi Developers

I have a NSTokenField witch on delegated method
    
- (NSArray *)tokenField:(NSTokenField *)tokenField shouldAddObjects:(NSArray *)tokens atIndex:(unsigned)index
{
	NSMutableArray *moddedTokens =[NSMutableArray array];
	
	NSEnumerator *enumerator = [tokens objectEnumerator];
	id token;
	while ( token = [enumerator nextObject] ) {
		NSRange range;
		range = token stringValue] rangeOfString:@" <"];
		if (range.location < 10000) {
			range.length = range.location;
			range.location = 0;
			[moddedTokens addObject: [token substringWithRange: range;
		} else {
			[moddedTokens addObject: token];		
		}
	}
	
	return moddedTokens;
}

transform my values witch are indicated on the NSTokenField.

On this method, i want my original value, but NSLog write nothing to the log.
The value from representedObject is for example "Philippe Regenass", but i want my value "Philippe Regenass <00714455464>"
    
- (NSMenu *)tokenField:(NSTokenField *)tokenField menuForRepresentedObject:(id)representedObject
{

	NSLog(@"tokenfiled: %@", [tokenField stringValue]);
	.....
}

Any idea? 

Best regards

Philippe Regenass

----
I'm just figuring this out myself, but here's some ideas:

I don't think you should transform the values in tokenField:shouldAddObjects:atIndex:. Instead, just implement tokenField:displayStringForRepresentedObject: and parse your full address string (which is the represented object) down to just the name there.  Each represented object should contain all the data you are representing for that token. (If you want, you could use tokenField:shouldAddObjects:atIndex: to validate, (as implied by the docs), to make sure the full string is in the right format.)

The represented object doesn't need to be a string, it could be a dictionary - that way you can parse the "<" formatted data to "name" and "address" keys only when you get new items from editing or pasting. At least that seems more reliable to me. To do that, implement full-string-to-dictionary conversions in tokenField:representedObjectForEditingString: and tokenField:editingStringForRepresentedObject:. In that case, you'd need to implement the two pasteboard-related delegate methods, too. -- PaulCollins
----
hi PaulCollins

thank you for your feedback. I use now own class as represented object. but i have difficult problem: i use autocomplete delegate from nstokenfield.
i search the typed string and found the person in address book. but i don't want return array with plain strings (e.g. Philippe Regenass <071 877 11 11>). when i do this, i must search the person again when the user insert the person to the token field.
any "hacks" or custom token fields available for this problem?

best regards

PhilippeRegenass

