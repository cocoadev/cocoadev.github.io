---
layout: page
title: VerticallyCenteringTableViewItems
---



I have 34px tall rows and they look bad with their text on the top of the row. Also, I'd like to change the color of the text on selected items to white. All of this needs to be done from within my General/NSTableView subclass.



Any ideas on how would I do this? Thanks!

----

If you really need to do this in your General/NSTableView subclass instead of, say, an General/NSTextFieldCell subclass, you could override frameOfCellAtColumn:row: to return an adjusted frame that's centered to the row height based on the text inside. As for the color changing, implement tableView:willDisplayCell:forTableColumn:row: on the table view's delegate to do [cell setTextColor:General/[NSColor whiteColor]] if [cell isHighlighted]. -- General/AndyMatuschak

*However, it really would be better to subclass General/NSTextFieldCell and draw the text centered from there. You can set the cell class in General/InterfaceBuilder for your table column. --General/JediKnil* Yes. That's a much better idea.


I use to set the _cFlags.vCentered = 1; in my subclass of General/NSTextFieldCell. Working in Tiger.

--Hans Gidlof

*Ooh, that's dangerous. -- General/AndyMatuschak*

Not if you encapsulate it with a try/finally block, tossing out the error if one occurs. That should make it work on existing systems, but 'turn off' the effect if things change in future versions of the OS.

----

Ok, I've managed to subclass General/NSTextFieldCell - now how do I set _cFlags.vCentered = 1; in the subclass? I'm not seeing anything about it in the docs so I don't really know how to use it.


Thanks again!

----

How do you set the cell class in General/InterfaceBuilder for your table column? RH

----

Standard warning.  You don't see it in the documents because it's an undocumented feature, which Apple reserves the right to change at any time in any way in the future, thereby possibly breaking your app. You should never rely on undocumented features as vital part of your code in shipping apps and always write protected blocks around such code so that if it fails to work, the app behaves as if the hack weren't there. Hence the warning about it being dangerous and the reply about try/finally. Clear? :-)

Anyway, in answer to your question, you *should* just be able to call     _cFlags.vCentered = 1; somewhere in your subclass such that it gets called  when the cell's created. You'll most likely get a compiler warning since variable names starting with an underscore are usually private (hence, not a publicly available property you're supposed to manipulate but can if you know it's there).  (Okay, I'm done adding to my reply every twenty seconds now.)

----

Uhh... why the hack?

This should work fine....

    - (void)drawInteriorWithFrame:(General/NSRect)cellFrame inView:(General/NSView *)controlView
{
    General/NSSize contentSize = [self cellSize];
    cellFrame.origin.y += (cellFrame.size.height - contentSize.height) / 2.0;
    cellFrame.size.height = contentSize.height;

    [super drawInteriorWithFrame:cellFrame inView:controlView];
}

*Yeah, I was thinking about that. The trouble is that this will simply move the text halfway up the cell, instead of centering it. The difference is that the **baseline** of the text is being moved, not the centerline. If you can figure out the height of the text beforehand (including wrapping, etc.), this might work. --General/JediKnil*

Yeah, that was a small version of what I'm currently using.  My current code (which also handles the ...) is below:

    - (void)drawInteriorWithFrame:(General/NSRect)cellFrame inView:(General/NSView *)controlView
{
    General/NSSize contentSize = [self cellSize];
    cellFrame.origin.y += (cellFrame.size.height - contentSize.height) / 2.0;
    cellFrame.size.height = contentSize.height;
	
	General/NSAttributedString *drawString = [self attributedStringValue];
	
	General/NSRange range;
	General/NSDictionary *attributes = [drawString attributesAtIndex:0 effectiveRange:&range];
	
	float maxWidth = cellFrame.size.width;
	float stringWidth = [drawString size].width;
	
	if (maxWidth < stringWidth)
	{
		int i;
		
		for (i = 0;i <= [drawString length];i++)
		{
			if (General/drawString attributedSubstringFromRange:[[NSMakeRange(0,i)]size].width >= maxWidth)
			{	
				drawString = General/[[NSMutableAttributedString alloc] initWithString:General/[drawString attributedSubstringFromRange:[[NSMakeRange(0,i-3)]string]stringByAppendingString:@"..."]attributes:attributes];
				[drawString autorelease];
			}
		}
	}
	
	[drawString drawInRect:cellFrame];
}

*The above code leaks memory in the '...' handling part.  I'm adding in autorelease.*

--General/AustinSarner

In 10.4+ The '...' handling in an General/NSCell subclass can be done by simply calling  [self setLineBreakMode:General/NSLineBreakByTruncatingTail] when setting up the cell

--Jim M<nowiki/>cGowan

----

Hello, I have been trying to implement changing the text color using: tableView:willDisplayCell:forTableColumn:row:. It works in that it changes the color once you have released the mouse button, but when you click on a row, the text color stays black until you release, then it changes to white. Can anyone help me out working around this?

UPDATE: As I look around at other apps, I see that this seems to be caused because in my app the row is selected immediately when it is clicked on, where as everything else doesn't select till you release the mouse button which seems to be the standard functionality. Now I guess I'm asking how I would go about disabling this and not selecting the row until the mouse is released.

-- Zeus

----

Zeus: See General/HowToAskQuestions and General/HowToUseThisSite ... you need to first **search** to see if your question has been answered elsewhere before posting. Then, if you need to post, make sure you're posting on a related page or create a new page. Your question has nothing to do with this page's title. Please don't dirty the wiki.

----

I did search this site, many times using the built in text search and google search to no joy. I asked on THIS page however, because the original poster already asked about the text color and was told to use: tableView:willDisplayCell:forTableColumn:row: way back in the first reply. 

-- Zeus

----

Anyone had any luck duplicating Mail's large cells yet?  by setting the cell class to General/NSBrowserCell I can get it to do everything except vertically centered text.  And since, even subclassing, there is only one draw method to override the text drawing would require drawing the whole cell again anyway -- better to just write a new class.
So, moving on the General/NSButtonCell, this looks good on paper but horizontally aligning the title is unresolved for me, and regardless I can't get the button to NOT draw any kind of background, ie be transparent except the image and the text.  I feel like I'm missing something but the docs are larger and more convoluted for this class.

Anyway, just checking in.

----

I just finished putting together an General/NSCell subclass that displays an icon with vertically centred text to the left of the icon, code below.  The icon parts could easily be removed to give just vertically centred text.

    
#import <Cocoa/Cocoa.h>

@interface General/TextAndIconCell : General/NSTextFieldCell 
{
	General/NSImage *icon;
}

- (void)setIcon:(General/NSImage *)newIcon;

@end

    
#import "General/TextAndIconCell.h"

void defineTextAndIconRects(General/NSRect cellRect, General/NSRect *imageRect, General/NSRect *stringRect, float stringHeight);

@implementation General/TextAndIconCell

- (id)init;
{
    self = [super initTextCell:@""];
	
	icon = nil;
	
    [self setEditable:YES];
    [self setScrollable:YES];
    [self setLineBreakMode:General/NSLineBreakByTruncatingTail];

    return self;
}

- (id)copyWithZone:(General/NSZone *)zone;
{
	General/TextAndIconCell *copy = General/[[TextAndIconCell allocWithZone:zone] init];
	[copy setAttributedStringValue:[self attributedStringValue]];
	[copy setIcon:icon];
	return copy;
}

- (General/NSColor *)textColor
{
	if([self isHighlighted])
	{
		return General/[NSColor selectedMenuItemTextColor];
	}
	else
	{
		return General/[NSColor controlTextColor];
	}
}

- (void)drawInteriorWithFrame:(General/NSRect)aRect inView:(General/NSView *)controlView
{
	General/NSRect imgRect, strRect;
	float strHeight = General/self attributedStringValue] size].height;
	defineTextAndIconRects(aRect, &imgRect, &strRect, strHeight);
	
	if([[self controlView] isFlipped])
	{
		[icon setFlipped:YES];
	}
	[icon drawInRect:imgRect fromRect:[[NSZeroRect operation:General/NSCompositeSourceOver fraction:1.0];
	
	General/self attributedStringValue] drawInRect:strRect];
}
	
- (void)selectWithFrame:([[NSRect)aRect inView:(General/NSView *)controlView editor:(General/NSText *)textObj delegate:(id)anObject start:(int)selStart length:(int)selLength
{
	General/NSRect imgRect, strRect;
	float strHeight = General/self attributedStringValue] size].height;
	defineTextAndIconRects(aRect, &imgRect, &strRect, strHeight);
	
	[super selectWithFrame:strRect inView:controlView editor:textObj delegate:anObject start:selStart length:selLength];
	[textObj setTextColor:[[[NSColor controlTextColor]];
}

- (void)setObjectValue:(id <General/NSCopying, General/NSObject>)object
{
	// change this part to suit your own use...
	if([object isKindOfClass:General/[NSDictionary class]])
	{
		[self setStringValue:[(General/NSDictionary *)object valueForKey:@"name"]];
		[self setIcon:General/[NSUnarchiver unarchiveObjectWithData:[(General/NSDictionary *)object valueForKey:@"image"]]];
	}
	else
	{
		[super setObjectValue:object];
	}
	
}
	
- (void)setIcon:(General/NSImage *)newIcon
{
	if(newIcon != icon)
	{
		[icon release];
		icon = [newIcon copy];
	}
}

- (void)dealloc
{
	[icon release];
	[super dealloc];
}

@end

void defineTextAndIconRects(General/NSRect cellRect, General/NSRect *imageRect, General/NSRect *stringRect, float stringHeight)
{
	float cellHeight = cellRect.size.height;
	
	General/NSDivideRect(cellRect, imageRect, stringRect, cellHeight, General/NSMinXEdge);
	
	*imageRect = General/NSInsetRect(*imageRect, 2.0, 2.0);
	*stringRect = General/NSInsetRect(*stringRect, 2.0, 0.0);
	
	stringRect -> origin.y += (stringRect -> size.height - stringHeight) / 2.0;
	stringRect -> size.height = stringHeight;
}


--Jim M<nowiki/>cGowan
