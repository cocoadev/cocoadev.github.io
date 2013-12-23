---
layout: page
title: GettingDataToAndFromNSDictionary
---

So, basically, if I want to send the data from a NSTextField to my dictionary, I do this code:

    [Dictionary setObject:[TextField stringValue] forKey:@"MyKey"];

I'm curious as to what I do with an NSTextView, however.  I try this same method (stringValue), and it doesnt work.


The other thing I'm trying to do is write back from the NSMutableDictionary to a couple different objects... Once again, I do this for a TextField:

    [TextField setStringValue:Dictionary objectAtIndex:[Table selectedRow objectForKey:@"MyKey"]];

I also want to be able to write back to the same NSTextView.  The other thing I want to be able to do is check a boolean variable in the dictionary, and set a checkbox accordingly.  Thanks for the help.

:LoganRockmore

----

Um, you can only send those messages to instansiated objects, not the class itself.... Go read a Cocoa book!

----

No, I know.  I just put those generic names in for the names of the objects.  I changed it.  Sorry.  :LoganRockmore

*Use     string instead of     stringValue. Remember to check superclasses when looking for useful methods. :) --JediKnil*

----

yeah, i put in     [Dictionary setObject:[TextView string] forKey:@"Notes"];, and that didnt work.  It just created a blank string for the key.  :LoganRockmore

----

use     [Dictionary setObject:[NSString stringWithString:[TextView string]] forKey:@"Notes"];

and if you want to put a BOOL value instead the dictionary:     [Dictionary setObject:[NSNumber numberWithBool:yourBoolValue] forKey:@"BOOL"];

----

Awesome, the stringWithString worked.  One more thing...  I have a date in my Dictionary, and I want to copy it to a TextField.  The field has a Date Formatter on it already... I put it in the Dictionary like this:      [dictionary setObject:[NSDate dateWithNaturalLanguageString:[dateTextField stringValue]] forKey:@"Date"];   How do I go about doing this?  :LoganRockmore

*Sorry about my previous advice, I really thought it would work. This time I'm sure, though: use     objectValue instead of     stringValue. The difference is whether or not to apply the formatter. --JediKnil.*

----

Great!  Everything works just peachy now.  Thanks everyone for the help.  :LoganRockmore

*Excellent! Discussion retired.*

