---
layout: page
title: TextlayoutWithAttributedStrings
---

This is still a work in progress (isn't everything) so bear with me. This is the bottom up view. For the top down view look at "Text System Architecture" at Apple http://developer.apple.com/techpubs/macosx/Cocoa/General/TasksAndConcepts/General/ProgrammingTopics/General/TextArchitecture/index.html

*The link above does not work anymore. Is this one the same document? http://developer.apple.com/documentation/Cocoa/Conceptual/General/TextArchitecture/ *

There are a variety of classes that work together to enable well formatted text. The basic classes are the General/NSAttributedString and its mutable counterpart General/NSMutableAttributedString.

An attributed String contains the string and a series of ranges that are associated with sets of attributes. You can see this for yourself if you call the description method of an attributed string to print it out or use "po" in the debugger. The attributes describe what kind of formatting is to be applied to the string. The attributes are valid for ranges within that string. 

The class is defined in General/FoundationKit and in General/AppKit so you will have to look up both documentations to get the whole picture. One important subclass is the General/NSTextStorage which can be obtained from a General/NSTextView with the 

-(General/NSTextStorage *)textStorage method.

If you are using a mutable attributed String, the attributes can be changed with a variety of functions that set or add single or multiple attributes. Single attributes consist of an attribute name (see the reference in General/AppKit,  General/NSAttributedStringAdditions) and a value. Multiple attributes are gathered in a General/NSDictionary and then applied. All functions that change attributes work over a General/NSRange. 

Most of the attributes are concerned with character formatting -- color, font, kerning -- but there is one attribute that controls paragraph formatting it is represented by an General/NSParagraphStyle object -- indentation, line spacing, ... -- 

One helpful utility function in General/NSMutableAttributedStringAdditions is 

-(void)applyFontTraits:range:

This changes the style of the current font without you the programmer having to select and create a new font object. E.g. you can change the font of your text to the bold type or the oblique very easily. A font trait is a simple string defined in General/NSFontManager.

----
The following Html Pages are pertinent

General/FoundationKit, General/NSAttributedString: http://developer.apple.com/techpubs/macosx/Cocoa/Reference/Foundation/ObjC_classic/Classes/General/NSAttributedString.html

General/FoundationKit, General/NSMutableAttributedString: http://developer.apple.com/techpubs/macosx/Cocoa/Reference/Foundation/ObjC_classic/Classes/General/NSMutableAttributedString.html

General/AppKit, General/NSAttributedStringAdditions: http://developer.apple.com/techpubs/macosx/Cocoa/Reference/General/ApplicationKit/ObjC_classic/Classes/General/NSAttributedString.html

General/AppKit, General/NSMutableAttributedStringAdditions: http://developer.apple.com/techpubs/macosx/Cocoa/Reference/General/ApplicationKit/ObjC_classic/Classes/General/NSMutableAttributedString.html

General/AppKit, General/NSTextStorage
http://developer.apple.com/techpubs/macosx/Cocoa/Reference/General/ApplicationKit/ObjC_classic/Classes/General/NSTextStorage.html

General/AppKit, General/NSFontManager
http://developer.apple.com/techpubs/macosx/Cocoa/Reference/General/ApplicationKit/ObjC_classic/Classes/General/NSFontManager.html

Last Change: Just corrected Typos

-- General/HaRald

----

General/NSParagraphStyle docs are at [http://developer.apple.com/documentation/Cocoa/Reference/General/ApplicationKit/ObjC_classic/Classes/General/NSParagraphStyle.html]. General/NSParagraphStyle controls aligment, line spacing, indentation and so on
