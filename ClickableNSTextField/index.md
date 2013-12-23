---
layout: page
title: ClickableNSTextField
---

I am trying to make a clickable URL using an NSTextField. I've seen Apple's example using an NSTextView, but it seems overblown. My basic assumption is that an NSTextField descends from NSControl, and I should be able to enable it and set up its action so my controller gets clicks. Here is my code. Are my assumptions just wrong?

    
[fiInfoURL setStringValue: [model infoURLStr:row]];     // fiInfoURL is an NSTextField
[fiInfoURL setEnabled:YES];
[fiInfoURL setTarget: self];
[fiInfoURL setAction:@selector(doInfoURL:)];
[fiInfoURL sendActionOn:NSLeftMouseDownMask];


I never see doInfoURL gettting invoked when I click. Is there something more I can do to make this work, or do I need to create a text view instead.

----

Is it possible that you do not yet know about the FieldEditor?  Every window has a shared text object, the FieldEditor, that handles real text editing (see FieldEditor for more info).  Try using Apple's example code to create a custom NSTextView subclass, then specify that an object of that class should be the field editor for your text field with the NSWindow delegate method     -windowWillReturnFieldEditor:toObject:.

----

Your assumption is incorrect; not every NSControl sends its action to its target when clicked. NSTextField, for example, sends its action when you press return, or tab out to another field, depending on how you configured it in IB. If you want a control that responds to mouse clicks, use an NSButton.

*He wants clickable URLs within a text field.  NSButton would not be appropriate.*

Where do you see that? He's setting the entire text field's contents to be the URL string, and he explicitly states that he wants to make a clickable URL using an NSTextField, not that he wants to embed a clickable URL within a larger body of text in an NSTextField. NSButton is entirely appropriate.

----

I assume a non-editable text field would work OK. How about subclassing and overriding     mouseDown? Would that work?

