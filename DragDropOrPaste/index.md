---
layout: page
title: DragDropOrPaste
---



Hi, gang. I want to intercept (and normalize the formatting of) text as it's dropped or pasted into an NSTextView. I don't want to normalize the entire contents as this is a performance issue with lots of text. I've thought of overriding     textStorageDidEndProcessing but this won't tell me whether the text has been dropped, pasted, or just typed. In short, I need to intercept the drop operation and the paste operation so I can normalize attributed strings (or just ignore plain old strings).

What's the best cocoa-friendly, non-evil approach?

----

normalize how? do you just want to ignore the attributes and apply your own? in that case, umm... just ignore the attributes and apply your own.

----

Thanks, but that's not what I'm asking. ;-) The normalizer routine is well-established in a production app for a long time, now. With that part, I'm quite familiair. ;-) I'm asking how to intercept the actual text being pasted and *get it to* the normalizer when it's a paste or drop operation. 

I've just found a cocoa-dev reference to     - (BOOL)readSelectionFromPasteboard:(NSPasteboard *)pboard type:(NSString *)type at http://www.cocoabuilder.com/archive/message/cocoa/2004/3/29/102873 

... which is a step forward I think, but I don't want to decide whether to approve a paste or drop, but rather to determine the thing to be pasted or dropped and modify it beforehand. Since I've discovered this method, I'm thinking I could copy and modify the pasteboard and pass that to super ... right? Would that be safe? I'm going to try it for myself in a moment anyway, but what I mean is, *is that the right way or is there a better way?*

----

Cocoa's text system is made up of four main parts:


*NSTextStorage - the object that contains the unicode text and string attributes like color and font info
*NSLayoutManager - the object that creates, positions and draws glyphs created using the characters and attributes contained in NSTextStorage objects
*NSTextContainer - layout regions (e.g. a page boundary) that make up a document's presentation
*NSTextView - the view that displays the text


If you only want the unicode text, you should look at subclassing NSTextStorage (look at the discussion on this class because someone has already posted a nice howto on the topic). If you would like to trap the raw data that in not normalized into UTF16, your best bet is to subclass NSTextView and override the     - (void)paste:(id)sender first responder action.

    
- (void)paste:(id)sender {
	NSPasteboard *pb = [NSPasteboard generalPasteboard];
	NSArray *types = [pb types];
	unsigned int i, count = [types count];
	id methods[] = {@"dataForType:", @"stringForType:", @"propertyListForType:"};
	for (i = 0; i < count; i++) {
		int m;
		NSString *type = [types objectAtIndex:i];
		for (m = 0; m < 3; m++) {
			id obj = [pb performSelector:NSSelectorFromString(methods[m]) withObject:type];
			if (!obj) continue;
			NSLog(@"[pb %@@\"%@\"]: %@", methods[m], type, [obj description]);
		}
	}
}


--zootbobbalu

oh yeah, if you want to trap the raw data from a drop operation then check out the category NSDraggingDestination and override the methods described in the NSDragging.h header.

