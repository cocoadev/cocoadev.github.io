---
layout: page
title: WhenToCallSetFrameOnAFieldEditor
---



Hi,

I want to have control over the frame of my field editor (a simple subclass of General/NSTextView) for my General/TextField subclass.

It seems that the field-editor frame is welded to that of its text-field. When the user starts editing, I want the field editor to draw itself in a slightly different position to the default, and so I have tried calling setFrame:.

Unfortunately the field-editor seems very very stubborn, and calling setFrame: on it doesn't budge it. I've tried changing the frame when the field-editor is created by windowWillReturnFieldEditor:forObject: but whatever I set it to, and whatever crazy General/NSRect I pass it, it gets ignored, and I've also tried calling setFrame on the field-editor from within the parent text-field class.

How do I wrench back control of my field-editor! I just want it to draw 2px higher than the parent textfield.

(I suppose I could hack around it, assuming that the field editor asks its 'owner' General/TextField for its frame so it can be the same, I could override the textfield's 'frame' method to return an offset frameRect to objects of the class of my field-editor... but I'd like to do things in a neater way, and considering the stubbornness of the field-editor, I'm thinking even this might not work).

Thanks.
