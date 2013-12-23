---
layout: page
title: HowToGetRidOfFocusRingAroundTextCell
---



Does anyone know how to completely disable focus ring around a cell in a table column when it's being edited? (Yes, I want to draw my custom focus ring).

I use     setFocusRingType:NSFocusRingTypeNone but instead of light-blue focus ring I get a white rect (which as it appears is always drawn under the focus ring in a edited textcell). It's not noticeable on white background in Finder but is clearly visible on black.

Nothing helps, I don't know how to get rid of this white rectangle.

http://img172.imageshack.us/img172/9490/picture14jw7.jpg

----

Post your code.

----

Not sure what you mean. Ok, here it is again. In a subclass of NSTextFieldCell I call on init: 
    
if (self = [super init])
{	
	[self setFocusRingType:NSFocusRingTypeNone];
	return self;
}
return nil;


And then in awakeFromNib in my Controller I set dataCell to that custom cell:

    
tableView tableColumnWithIdentifier:@"names"] setDataCell:customCell]


Blue focus ring dissapears, but white rect stays with me.

----

Have you removed the border? Removed background drawing? There are a lot of settings you can change.

----

Yes, I did. But it doesn't help.
    
[self setBordered:NO];
[self setBezeled:NO];
[self setDrawsBackground:NO];

Since white border appears when editing cell, I think it belongs to the field editor... And then I need to provide my custom field editor in order to get rid of the white rect.

----
Be carefu, I've run into weird issues where the Accessibility API insits upon drawing [[NeXT-style focus rectangles even when I explicitly tell the field editor not to draw anything.

