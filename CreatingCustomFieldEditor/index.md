---
layout: page
title: CreatingCustomFieldEditor
---

**See CustomFieldEditor for working example code for setting a custom FieldEditor.**

----

I have a situation in my application where I want to modify a certain text field's drag-and-drop behavior. It works as a search field, so I want drags onto the field to replace the text, rather than appending to it. There is a custom NSTextField subclass which handles this when the text field is not selected, but when it is, I have to deal with the FieldEditor. I created a custom NSTextView subclass which implements the desired drag-and-drop behavior, but I'm having a hard time making it work. I tried code like this in my window's delegate:
    
- (id)windowWillReturnFieldEditor:(NSWindow *)sender toObject:(id)anObject
{
	if(anObject == searchTextField)
	{
		DropReplacingTextView *textView =
			[[DropReplacingTextView alloc] init];
		[textView setFieldEditor:YES];
		[textView setDelegate:anObject];
		return [textView autorelease];
	}
	else
		return nil;
}

It works, in that my custom text view is used as the field editor, but it doesn't work very well. Namely, there is no focus ring drawn, and my text view doesn't receive text changed notifications, which is important. In the meantime, I am using this really nasty hack:
    
- (id)windowWillReturnFieldEditor:(NSWindow *)sender toObject:(id)anObject
{
	NSTextView *textView = [sender fieldEditor:YES forObject:self];
	if(textView)
	{
		if(anObject == searchTextField)
			textView->isa = [DropReplacingTextView class];
		else if(textView->isa == [DropReplacingTextView class])
			textView->isa = [NSTextView class];
	}
	return textView;
}

It works perfectly, but for obvious reasons I'd prefer to use the first approach, if it can be made to work. Does anybody know exactly what steps must be taken to turn an NSTextView into a proper FieldEditor?

-- MikeAsh

----

I think your problem is you're creating a new autoreleased DropReplacingTextView each time the window asks for the field editor. Try doing it in     awakeFromNib and keeping the instance around. This is how I'm doing it and I get a focus ring and notifications.

----

Whoever you are, I owe you a beer. That was it, works perfectly now. It seems very strange, though. I guess the error was because the text view wasn't retained by anything, but wouldn't inserting it into the view hierarchy retain it? Oh well, that's all theoretical; hanging on to it instead of re-creating it fixed it. Thanks a lot! -- MikeAsh

*Another thank you, this post saved me hours of hair wrenching. - Charlie*

*Just thirding this as probably one of the least intuitive but most critical fixes in a while - thanks.  I only which I'd found this about 3 hours ago...

----

It makes sense, I guess - you're not really inserting something into the view hierarchy; the window is telling you it's going to provide a field editor, and giving you a chance to provide one of your own, instead. If you provide it, it's up to you to retain it. Although that doesn't explain why it apparently still doesn't work if a non-autoreleased object is returned from this method (See below)

----

Apple gives this example as to how to swap in a new field editor:

    

- (id)windowWillReturnFieldEditor:(NSWindow *)sender toObject:(id)anObject
{
    if ([object isKindOfClass:[NSTextField class]])
    {
        return [[MYCustomFieldEditor alloc] init];
    }
    return nil;
}



But this in fact **DOES NOT WORK**!  Sure, it swaps in the new field editor, but its useless since changes don't register when using bindings in an NSTableView.  Basically, you click on the cell, your custom field editor pops up, very nice, you hit return and... the original value of the cell come back!  Clicking away doesn't work either.  Now, I checked the default field editor and it is in fact an NSTextView, so I tried simply this:

    

- (id)windowWillReturnFieldEditor:(NSWindow *)sender toObject:(id)anObject
{
    if ([object isKindOfClass:[NSTextField class]])
    {
        return [[NSTextView alloc] initWithFrame: NSMakeRect(...)];
    }
    return nil;
}



STILL doesn't work.  Now, I'm certain that the "missing link" takes place in [window fieldEditor: forObject:] since all I've touched is the windowWilLReturnFieldEditor toObject method, returnign the same class as the normla implementation would, and the system breaks.

Does anyone know how to solve this?

-FranciscoTolmasky

----

see above discussion initiated by MikeAsh

I think one major problem in the code above is that you didn't call setFieldEditor:YES.  That probably explains your difficulties when users press return.

