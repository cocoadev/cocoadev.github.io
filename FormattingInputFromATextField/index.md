---
layout: page
title: FormattingInputFromATextField
---

I'm a little stuck right now. I've been subclassing NSTextField and implementing -textDidChange: , handling formatting that I need to do in that function (stripping unsupported attributes, etc.) However, some of the formatting I need to do involves replacing substrings (e.g. "#SA") with images. I've been using the following code:

    
for (/* each instance of #SA */) {
	[string replaceCharactersInRange: oldRange withAttributedString: imageString];
	[string addAttribute: GTStringRepresentationOfImageAttributeName value: @"#SA" range: imageRange];
}


The trouble starts as soon as -textDidChange: ends. Because I've changed the length of the attributed string, the field editor chokes with exceptions like this one:
2006-06-22 14:35:39.224 mFurc[6785] *** NSRunStorage, _NSBlockNumberForIndex(): index (3) beyond array bounds (1)

Is there a better way or place where I can reformat attributed text--one that will allow me to make changes to the length of the text without throwing exceptions? I'm sure there's got to be, I just don't seem to be hitting the right Google keywords to find it.

Thanks much. - JonathanGrynspan

----

I think you're going about it the wrong way. Try learning about formatters and implementing it that way [http://www.devworld.apple.com/documentation/Cocoa/Conceptual/DataFormatting/Articles/Formatters.html]

----
Unfortunately, formatters don't fully support attributed strings. Rather, their methods (e.g. -editingStringForObjectValue:) almost all return NSString, which makes the class useless for what I need to do.

