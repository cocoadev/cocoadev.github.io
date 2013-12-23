---
layout: page
title: ButtonsInTableOrOutlineViews
---

Here's a simple example of how to incorporate a column of checkboxes in an outline or table view.

This project does nothing more than serve as an example of how to hook things up so that an NSTableView or an NSOutlineView can have a column of NSB**'uttonCell objects.

The example uses Java.

http://homepage.mac.com/wallbond/iblog/C774289658/E478325465/index.html  (Note: ThisLinkIsBroken)

----

Checkboxes in a NSTableColumn are super easy. In IB, drag an NSButtonCell onto a table column, and give the table column an identifier. Then, in the tableView:objectValueForTableColumn:row: method in your datasource, return either [NSNumber numberWithInt:NSOnState] or [NSNumber numberWithInt:NSOffState] to set a checkbox's value. Then you can use [anObject boolValue] to get the checkbox's value from the tableView:setObjectForTableColumn:row method. Setting up the NSButtonCell via IB works for 10.2 and greater, or you could do it manually using setDataCell on a table column in the awakeFromNib method. --KevinWojniak

The solution referenced above uses the awakeFromNib approach. Good to know about the "all-IB" approach though. I didn't know this
was possible.

----

How can I implement icons and text into a NSCell of NSTableView? For example when you have a video inside of iTunes it will display an icon along with the name.

----

See the NSTableColumn method     setDataCell: ( NSCell * ) aCell.

See also the documentation for NSImageCell. A cell containing both 'text' and images may have to draw the glyphs,
rather than load the characters as text.

Note that iTunes is a Carbon application, so you may not get exactly the same appearance in Cocoa, no matter what you do.

----

If all you want is to mix a few inline graphics with your text, adding graphics to an NSAttributedString and using the default NSTextFieldCell is the way to go.

----

Can anyone post an example of how to create an NSAttributedString with an image? And how would you be able to make the image go all the way to the right of the column?

----

See the NSTextAttachment page where a handy example was waiting.  As for text alignment, that's handled by NSTextView and I can't think of an easy way to implement that.

*Actually, text alignment is handled by the NSParagraphStyle class, which can be attached as an attribute to an NSAttributedString.*

----

How do I get both a String and a little button in a cell in an NSTableView? (Like iTunes manages?)

At first I thought it would be simple to just subclass NSButtonCell, and draw a string in.

I've got about half way with this approach, but I can't seem to keep the button 'hitzone' properly away from my text, I have a messy hack using     - (BOOL)trackMouse:(NSEvent *)theEvent inRect:(NSRect)cellFrame ofView:(NSView *)controlView untilMouseUp:(BOOL)untilMouseUp but it really is messy, and doesn't work like a button should in some circumstances - it's a very crude approximation.

Does anyone have any info on going about such a cell properly?

Would I be better off perhaps with just starting by subclassing NSActionCell rather than the ButtonCell?
----

See http://www.stepwise.com/Articles/Technical/NSCell.html for an example.

----

you can add your graphics into the cell when it is drawn with something like this:
    
- (void)tableView:(NSTableView *)aTableView willDisplayCell:(id)aCell forTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex
{
	NSColor *txtColor = [NSColor colorWithCalibratedRed:.1 green:.1 blue:.1 alpha:1.0];
		NSDictionary *txtDict = [NSDictionary dictionaryWithObjectsAndKeys:txtColor, NSForegroundColorAttributeName, @"1", NSSuperscriptAttributeName,nil];
		NSMutableAttributedString *attrStr = [[[NSMutableAttributedString alloc]
        initWithString:[aCell stringValue] attributes:txtDict] autorelease];

		NSFileWrapper *fwrap = [[[NSFileWrapper alloc] initRegularFileWithContents:[[NSImage imageNamed:@"my_image"] TIFFRepresentation]] autorelease];
		NSString *imgName = @"my_image.tif";
		
		[fwrap setFilename:imgName];
		[fwrap setPreferredFilename:imgName];
		NSTextAttachment *ta = [[[NSTextAttachment alloc] initWithFileWrapper:fwrap] autorelease];
		[attrStr appendAttributedString:[NSAttributedString attributedStringWithAttachment:ta]];

		[aCell setAttributedStringValue:attrStr];		
}

--EcumeDesJours

