---
layout: page
title: ReverseIndentedNSTextField
---



I have a grid of NSTextField<nowiki/>s. The fields wrap and change height based on the number of lines of text in them. I would like to automatically indent lines of text after the first line to visually distinguish fields in the same column from each other (i.e. so it is obvious that a 3-line field is in fact only one field, not 3 different ones).

I've looked over (and been somewhat overwhelmed by) Apple's Text Layout documentation (http://developer.apple.com/documentation/Cocoa/Conceptual/TextLayout/index.html) and delved into the documentation for what seem to be the relevant classes: NSLayoutManager, NSTypesetter, NSTextContainer, NSTextView. I've tried a few different solutions but nothing works quite right.

I'm fairly sure I need to be using a subclass of NSTextContainer, let's call it ReverseIndentationTextContainer. It contains one instance variable: a float to store the indentation level (in pixels) and relevant accessors, and it overrides lineFragmentRectForProposedRect as follows:
    
- (NSRect)lineFragmentRectForProposedRect:(NSRect)proposedRect sweepDirection:(NSLineSweepDirection)sweepDirection movementDirection:(NSLineMovementDirection)movementDirection remainingRect:(NSRect *)remainingRect;
{
	if (proposedRect.origin.y > 0) {
		proposedRect.origin.x += [self indentation];
	}
	return [super lineFragmentRectForProposedRect:proposedRect sweepDirection:sweepDirection movementDirection:movementDirection remainingRect:remainingRect];
}


So the question now is how to hook this into the app so it gets used.

Since we're dealing with textfields, it seems to make sense that we need to attach it to the field editor. I tried simply calling replaceTextContainer: on the field editor when editing of the fields begins, in the textfield delegate's controlTextDidBeginEditing method. It works beautifully until editing finishes, then the lines are no longer indented.

Perhaps we need to subclass NSTextView and create a custom field editor for the relevant fields? I also tried doing this. My subclass looks as follows:
    
@interface ReverseIndentationFieldEditor : NSTextView {
	ReverseIndentationTextContainer *textContainer;
	float REVERSE_INDENTATION = 20.0;
}
@end
@implementation ReverseIndentationFieldEditor
-(id)init;
{
	if ( self = [super init] ) {
		[self setFieldEditor:YES];
		textContainer = [[ReverseIndentationTextContainer alloc] init];
		[textContainer setIndentation:REVERSE_INDENTATION];
		[self replaceTextContainer:textContainer];
	}
	return self;
}
-(void)dealloc;
{
	[textContainer release];
	[super dealloc];
}
@end


I tried returning this field editor from my window delegate's windowWillReturnFieldEditor method as follows:
    
- (id)windowWillReturnFieldEditor:(NSWindow *)sender toObject:(id)anObject;
{
	if ( [anObject cell] representedObject] isKindOfClass:[[[ReverseIndentContent class]] ) {
		if (!reverseIndentFieldEditor)
			reverseIndentFieldEditor = [[ReverseIndentFieldEditor alloc] init];
		return reverseIndentFieldEditor;
	}
	return nil;
}



The latest thing I've tried doing is subclassing NSTextField and NSTextFieldCell so I can hook in my NSTextView subclass through them, overriding the following method in NSTextFieldCell:
    
- (NSText *)setUpFieldEditorAttributes:(NSText *)textObj;
{
	textObj = [super setUpFieldEditorAttributes:textObj];
	ReverseIndentationTextContainer *container = [[ReverseIndentationTextContainer alloc] init];
	[container setIndentation:20.0];
	[(NSTextView*)textObj replaceTextContainer:container];

	return textObj;
}

My NSTextField subclass is empty and exists solely so I can call setCellClass: on it and assign it my custom cell class.

Both of these last two attempts have the same problem as my first solution; after the field is no longer being edited it loses the formatting (nevermind the fact that the above code is horribly wasteful and allocates a new field editor for each textfield - I'm still just experimenting). It also doesn't seem to honour the setWraps:YES that's been called on the NSTextField<nowiki/>s.

So, obviously I have no idea what I'm doing.

Thanks in advance for any help.
-Greg

----
I caught this on the list and sent a reply but I'll dupe it here...

I would use NSAttributedStrings instead. Make a NSMutableParagraphStyle with 5 or 10 for its headIndent and reuse/apply that to each NSAttributedString before giving them to your text fields...

NSParagraphStyle, -headIndent ...
"Returns the distance in points from the leading margin of a text container to the beginning of lines other than the first."

