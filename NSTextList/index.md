---
layout: page
title: NSTextList
---



I'm trying to wrap my head around NSTextList and finding the documentation a bit frustrating. I understand that they "appear as attributes on paragraphs, as part of the paragraph style" but I'm not sure quite what that means for manipulating them programmatically. Does anybody have a good understanding of this topic and could you possibly explain its use any more clearly than the documentation?

----

NSTextList is a paragraph attribute that indicates the text is contained within some type of (un)ordered list. Introduced in OS X 10.4 (Tiger), it usually appears when importing HTML text into an NSAttributedString. In other words, given the following block of HTML:

    
<ol>
<li>My list item</li>
</ol>


If you create an attributed string with this HTML using     initWithHTML:documentAttributes: it will result in a single paragraph of text whose paragraph attribute will contain one instance of NSTextList. If you asked that object for its markerFormat, you would get back     @"{decimal}". NSTextList attributes are used by the NSLayoutManager and NSTypesetter classes during layout to adjust the paragraph margins, insert the leading "1." string, etc.

This is a new addition to the Cocoa text system in Tiger, so it's not just limited to imported HTML text. To see this in action, create a new RTF (not plain text) document in TextEdit. You'll see a "Lists" pop-up in the RulerView; you apply list attributes to paragraphs just like alignment and spacing.

Some possible programmatic applications: change the NSTextList object on an existing paragraph (they're immutable objects) to switch from decimal to roman numbers; add the attribute to existing paragraphs to create an ordered list.

----

Thanks. I understand what the docs say about what it *is*, but there's very little substance about how to actually *use* it. That's what I'm after.

----

Text list styles are attributes of a paragraph of text. That is to say NSTextList objects live inside NSParagraphStyle objects (you can read them with     [paragraphStyle textLists], and set them in an NSMutableParagraphStyle using     [mutableParagraphStyle setTextLists:textListArray]), and NSParagraphStyle objects are attributes of ranges of characters in an NSAttributedString. There is sample code elsewhere in this Wiki for manipulating paragraph styles--start at TextlayoutWithAttributedStrings--and in the Cocoa documentation at http://developer.apple.com/documentation/Cocoa/Conceptual/AttributedStrings/index.html

To "use" NSTextList, create one or more NSTextList objects--multiple objects would be required to represent embedded lists--and put them into an NSArray. Give this array to your NSMutableParagraphStyle using     setTextLists:, and set the paragraph style attribute of some range of text to this object using     [string addAttribute:NSParagraphStyleAttributeName value:mutableParagraphStyle range:selectedRange]. That is all you need to do... all of the formatting and layout of the lists is handled at display time by the NSTextView.

----

Thanks. I have a few specific questions, then:

 - NSParagraphStyle always contains NSTextList objects; a text list never spans paragraphs (even though a new line is required to separate them). Correct?

 - You said multiple NSTextList objects are requred to represent embedded lists. Do you mean each 'item' in the list is an NSTextList item? I'm sure I'm misunderstanding you, since the name doesn't seem to suggest that. I'm guessing you may be referring to 'sublists' - like "Item A has Subitem 1, Subitem 2. Item B has Subitem 1, Subitem 2, Subitem 3, ....." Or are you talking about none of the above?

I guess I should explain that I'd like to programatically create bulleted lists in a text view based on data acquired elsewhere. I'd like to spit out a brief introductory paragraph, the bulleted list (with 'subitems'), followed by a closing paragraph. How would I designate a specific range of text as a list, or even as a list item?

*How about you create a list, load it into an NSAttributedString, then examine it by printing its description and twiddling with its bits using the debugger? It seems to me that all of your questions could be answered by looking at an example of its use.*

----

I don't mean this as an attack, but I feel it's important to point out that the documentation should explain something thoroughly rather than a developer being expected to reverse-engineer examples. This should be documented, not rediscovered by each developer wishing to use it.

----

I looked at the documentation for NSTextList and it seems quite clear. This doesn't necessarily mean that you are at fault, of course, but I don't think it's the docs' fault either. Sometimes you just have to use a different way of learning things. If you don't feel the docs are sufficient, it's possible they're lacking (although I don't buy it) or it's possible you just don't get it, in which case looking at an example can be a good way to bypass whatever blockage your brain might have.

My point is, if someone says, "look at an example", it's not an excuse for poor docs, it's just another way to learn. Learning from examples is a very good way to understand things.

----

Someone above said that after adding the appropriate NSTextList objects to the NSParagraphStyle spanning the list text, "all of the formatting and layout of the lists is handled at display time by the NSTextView." However it doesn't seem, from my playing with it, that the NSTextView will actually insert the list markers for you. There are methods to help you figure out where the markers go and what they should be such as     itemNumberInListText:atIndex: and     listMarkerFor:item: but you have to--as far as I can tell--write the code yourself to put them in. Or am I just missing something?

----

I've been trying to figure out how to use NSTextList too, and failing to get anything to happen when I set the text lists of my NSParagraphStyle object. The docs are incredibly unhelpful. Has anyone figured out what exactly is necessary to get the NSTextView to recognize the text lists?

----

One thing that's not mentioned here is that each string/item in the list probably needs to have a newline at the end....otherwise the paragraphStyle doesn't apply. Please note that I haven't used NSTextList, I'm just assuming it's similar to NSTextTable (and friends)...

Untested...
    
{
NSMutableAttributedString *string = [[NSMutableAttributedString alloc] initWithString:@"item1\nitem2\nitem3\n"];
NSMutableParagraphStyle *paragraphStyle = [[NSParagraphStyle defaultParagraphStyle] mutableCopy];
NSTextList *list = [[NSTextList alloc] initWithMarkerFormat:@"({decimal})" options:NSTextListPrependEnclosingMarker];

    [paragraphStyle setTextLists:[NSArray arrayWithObjects:list, nil]];
    [string addAttribute:NSParagraphStyleAttributeName value:paragraphStyle range:NSMakeRange(0, [string length])];

textView textStorage] appendAttributedString:string];

    [list release];
    [paragraphStyle release];
    [string release];
}


*I guess that doesn't really work. hmm. Maybe someone can fix it so we have a working example?*

----

I had trouble finding how this works as well, but eventually got it working so I thought I'd post an example here. Note that [[NSTextList does not insert anything into the text, so you still have to insert bullets and tabs yourself. NSTextList just tells the Cocoa text system to treat this as a list. If you apply an NSTextList style to text that does not look like what the text system expects, the text system will ignore the attribute. Anyway, on to the working example:

    
	NSMutableParagraphStyle *style = [[NSParagraphStyle defaultParagraphStyle] mutableCopy];
	NSTextList *list = [[NSTextList alloc] initWithMarkerFormat:@"{disc}" options:0];
	NSString *bullet = [NSString stringWithFormat:@"\t%@\t", [list markerForItemNumber:1]];
	[style setTextLists:[NSArray arrayWithObject:list]];
	NSDictionary *attrs = [NSDictionary dictionaryWithObject:style forKey:NSParagraphStyleAttributeName];
	NSAttributedString *toAdd = [[NSAttributedString alloc] initWithString:bullet attributes:attrs];
	NSTextStorage *ts = [textView textStorage];
	NSRange selection = [textView selectedRanges] objectAtIndex:0] rangeValue];
	[ts replaceCharactersInRange:selection withAttributedString:toAdd];


[[ChadSellers

