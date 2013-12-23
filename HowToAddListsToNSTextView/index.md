---
layout: page
title: HowToAddListsToNSTextView
---



I've been working with NSTextView, trying to make it suitable for
outlining, and been running into a number of problems. There is a
class, NSTextList, wish would seem from the documentation to create
list formatting. However, in reality it does not such thing. So my
first question is, is there a simple way to programatically add a list
into a NSTextView, like clicking the list button on the ruler does?
I've found a number of mailing list posts asking this question, but I
have not yet found an answer.

Having failed so far to find a simple way to do this and have the
NSTextView manage my lists, I've subclassed it and overridden keyDown
to handle the backspace, enter, and tab keys. I've found a bizarre
behavior, though, with inserting into the NSTextStorage this sequence:
@"\t{bullet}\t", where {bullet} is the unicode for a bullet, and
setting the NSParagraphStyleAttribute with a NSTextList. I'm using
code that amounts to essentially this:

    
self textStorage] beginEditing];
[[NSMutableAttributedString *text = [[NSMutableAttributedString
alloc]initWithAttributedString:[self textStorage]];

ps = text attributesAtIndex:[self selectedRange].location-1 effectiveRange:&effectiveRange] objectForKey:[[NSParagraphStyleAttributeName];
ps = text attributesAtIndex:[self selectedRange].location-1 effectiveRange:&effectiveRange] objectForKey:[[NSParagraphStyleAttributeName];
if(ps == nil)ps = [NSParagraphStyle defaultParagraphStyle];
NSMutableParagraphStyle *mps = [ps mutableCopy];
NSTextList *listItem = [[NSTextList alloc] initWithMarkerFormat: @"{circle}" options:nil];
if([mps textLists] == nil)
{
       [mps setTextLists:[NSArray arrayWithObject:listItem]];
}
else
{
       NSArray *newList = mps textLists] mutableCopy];
       [newList addObject:newList];
       [mps setTextLists:newList];
}
[[NSMutableString *tabs = [@"\t" mutableCopy];
for(int i = 0; i < currentIndentation; i++)
{
       [tabs appendString:@"\t"];
}
[text appendAttributedString: [[NSAttributedString alloc]
initWithString:[NSString stringWithFormat:@"%@%@\t", tabs, [listItem
markerForItemNumber:1]]
attributes:[NSDictionary dictionaryWithObject:mps
forKey:NSParagraphStyleAttributeName]]];
self textStorage] setAttributedString:text];
[[self textStorage] endEditing];


For some reason, the [[NSTextView seems to recognize it as a list, which
some weird behavior, including crashes if the user tries to press return inside 
the list. It also adds an extra line every time the user presses enter. If I don't 
add the NSTextList, it no longer shows this behavior.

I've been working on this problem for a while now, and am now pretty
stuck. The documentation surrounding NSTextList is terrible, and there
is no (AFAIK) documentation about how NSTextView handles lists. Thanks
in advance for any help.

