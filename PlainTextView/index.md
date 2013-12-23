---
layout: page
title: PlainTextView
---

This page was dying in a spiral of political mess, it has been refactored into a NSTextView plain text editing tutorial. (If you're interested in the debate see the page history)

To create a plain text formatted NSTextView you must do the following:

1. Make a new NSTextView in your nib, select it and show the object inspector.

2. Go to the attributes tab and set these settings: (any settings not mentioned are up to your digression)


Selection: Character
Behaviour: Editable (yes) Selectable (yes) Visible Ruler (no) Field Editor (no)
Allows: Rich Text (no) Undo (yes) Graphics (no) Image Editing (no) Non-contiguous Layout (no)
Uses: Font Panel (no) Ruler (no)


3. Place this code into your project: 
    
[textView setTypingAttributes:[NSDictionary dictionaryWithObjectsAndKeys:[NSFont fontWithName:@"Monaco" size:10.0], NSFontAttributeName, nil]];


-- Thats it. Your text view will now automatically format drag and drop, clipboard pastes, and direct typing input, in plain text format.

