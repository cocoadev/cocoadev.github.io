---
layout: page
title: NSTextFieldVersusNSTextView
---




(Newb here) I've cobbled together a little experimental program where if you make a selection of text from a text view, it automatically dumps the text in an NSTextField elsewhere in the same window.

(I tried making the text go into an NSTextView, but the newly built app would always crash on any attempt and Xcode would warn me before the end of the build that NSTextView may not be able to accept or recognize the new text -- anyone know why?)

Anyway my problem is, yes, the text does show up in the NSTextField upon selection of text in a nearby text view, but when the selection is deselected, the new text in the NSTextField goes away too.  How do I make it stick?  And even better, how can I make this work in an NSTextView, where I could give the user more choices, like being able to edit and save.

The key part of the code I have in the controller is this:

    
- (void) textViewDidChangeSelection: (NSNotification *) aNotification
{

        NSString *selected, *placeholder;
	
	selected = [self selectedText];

	placeholder = selected;

        [outputField setStringValue: placeholder];
	    
}


The above is a variation on something I'd seen somewhere else, so I'm really not sure how it can be improved.  TIA.

Max

----

What is happening is that when you deselect the text, the notification is posted again, and the string being sent to outputField is empty, because nothing is selected.

You could use something like this:

    
- (void)textViewDidChangeSelection:(NSNotification *)aNotification {
	NSString *selected = [[NSString alloc] init];

	selected = [self selectedText];

	if ([selected isEqualToString:@""]) { // or you also could use if ([selected length] == 0)
		// Nothing is selected, so we dont change the string value of outputField
	} else {
		// Text has been selected, so we refresh the string value on the textfield
		[outputField setStringValue:selected];
	}
}


Jacob

----

The reason your code breaks with an NSTextView as the outputField is that NSTextView doesn't respond to the same messages. You'll need to write this for an NSTextView:

    
[outputField setString:selected];

