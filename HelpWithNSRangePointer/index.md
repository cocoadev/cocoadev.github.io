---
layout: page
title: HelpWithNSRangePointer
---

My program sometimes crashes when the following code is called and I can't figure out why.  I get a sigbus error from NSIntersectionRange().  When I break on my method and try to step through it everything is fine, the program doesn't crash.  Can someone with a better understanding of pointers help me figure this out?

The code below is supposed to remove the NSLinkTextAttribute from an NSAttributedString in my textView.

    
- (IBAction)removeAnnotation:(id)sender {

	if ( [textView selectedRange].length == 0) {
		return;
	}

	// if it's the whole range remove it from the annotations array
	NSRangePointer annotationRange;
	// if this returns nil the selected text is not linked 
	// this also fills annotationRange
	id attribute = textView textStorage] attribute:[[NSLinkAttributeName 
		atIndex:[textView selectedRange].location
		longestEffectiveRange:annotationRange
		inRange:NSMakeRange(0,textView textStorage] length])];
		
	if (attribute == nil) { // should never happen
		return;
	} else {

		if ([[NSEqualRanges([textView selectedRange], *annotationRange)) {
			AVNRannotation *thisNote = textView textStorage] 
				attribute: [[NSLinkAttributeName
				atIndex: [textView selectedRange].location
				effectiveRange:NULL];
			
			[self willChangeValueForKey:@"annotationList"];
			[self document] annotations] removeObject: thisNote];
			[self didChangeValueForKey:@"annotationList"];
		}
	}
	
	[[textView textStorage] removeAttribute:[[NSLinkAttributeName range:[textView selectedRange]];
	self document] updateChangeCount:[[NSChangeDone];	
}



----

Both the original post and the reply above belie a horrible misunderstanding of the situation. (No offense, it's just the kind of thing that will make horrible, hard-to-find bugs.)

The     attribute:... method asks for an NSRangePointer because it's *returning* an NSRange. Thus, you should not be passing it a variable NSRangePointer, initialized to nil or otherwise. What you should be doing is passing it a pointer to an existing NSRange, like this:
    
NSRange annotationRange;
id attribute = textView textStorage] attribute:[[NSLinkAttributeName 
	atIndex:[textView selectedRange].location
	effectiveRange:&annotationRange];


The range will be returned to the     range variable.

When something asks for a pointer, you must always give it a pointer that is either nil or points to something interesting. If the pointer is used to return information, it must point to some space for that information.
----
To whom:

Thanks for the info.  That worked perfectly.  I have been able to get along with very little knowledge of C so far but this one bit me.  I edited your post for clarity. *Thanks for correcting my laziness, it's much easier to read now.*

----

Pointers are **the** most important thing you could possibly know about C. If you don't know about them, that will be the cause of about 70% of your problems.

