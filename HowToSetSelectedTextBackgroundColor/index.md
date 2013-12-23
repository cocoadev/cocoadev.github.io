---
layout: page
title: HowToSetSelectedTextBackgroundColor
---

Does anyone know how you can change the background color of only the selected text?  I searched the NSTextView.html / NSTextField.html / NSColor.html and none of them explain how to do it.  Through NSColor.html you can find the color of the background of the selected text, but there's no way to set it.  I think I'm just missing something.  Can someone please enlighten me?  Thanks.

Cheers,
 - Daniel

----

Well, I found something but it doesn't hold (hence the 'temporary' part of the code).  I'm wondering if anyone knows how to permananently change the background color of the selected text.  This is what I'm using right now, but I need something that isn't temporary.

    
textField layoutManager] addTemporaryAttributes:[[[NSDictionary dictionaryWithObject: highlightColor forKey: NSBackgroundColorAttributeName] forCharacterRange: [textField selectedRange]];


Cheers,
 - Daniel

----

    [textView textStorage] addAttribute:NSBackgroundColorAttributeName value:highlightColor range:[textView selectedRange]] might be what you're looking for if you're using an NSTextView.  However, it seems kinda funny that you're manually highlighting selected text when both NSTextField and NSTextView do that automatically.  -- Bo

----

Not so silly.  Things like notepad programs use a feature like this to simulate a highlighter pen and allow a user to color-code sections for later attention.  Oh, and thanks for the answer. ;) --cp

