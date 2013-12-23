---
layout: page
title: FilteringAttributesFromNSTextStorage
---



I'm trying to write a routine to normalize text in a text view. I'm using something like the pseudo code below. I'd like to be able to provide a list of the stuff to *keep* and remove everything else for efficiency's sake. I'm only interested in setting a standard font, size, and paragraph style. 

    
effectiveRange = NSMakeRange(0,0);
NSDictionary * attributes;
while (NSMaxRange(effectiveRange) < sourceString string] length]) 
{
	// Get the attributes for the current range
	attributes = nil;
	attributes = [sourceString attributesAtIndex:[[NSMaxRange(effectiveRange) effectiveRange:&effectiveRange];

	// ... do stuff here
}


How do I go about filtering out unwanted attributes without modifying the rest? Do I actively 'removeAttribute:atRange:'?  ... what 'stuff' should I actually 'do' there? Is the code above the most efficient for iterating through the attributes?

Suggestions?

----

That sounds like it'd work. You could try     setAttributes:range:, passing nil for the attributes you want to remove. NSFontManager has various     convertFont... methods you might find useful as well.

*Never mind the post I just replaced - it's been a long day and I see how incredibly brain-dead I'm being. Thanks a lot for this - you can't set 'nil'  on a dictionary, so just create a dictionary with only the attributes you want, pre-set. For each range, check for the ones you want to keep and adjust the individual attributes in the dictionary and *set* the attributes for that range to the 'normalized' values. Thanks again - I was making this way too hard, which is the first clue I'm being an idiot. ;-)*

