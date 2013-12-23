---
layout: page
title: SpecialDisplayCharactersInTextView
---



March 20, 2006

I'm looking to display special symbols into a text view, following the text, but uneditable and unselectable. These symbols will designate paragraph breaks, spaces, hyperlinks, etc., and therefore should not be deleted by backspacing, cutting, etc., like the 'formatting characters' in word processor applications.

For an example, go to http://en.wikipedia.org/wiki/Cocoa_%28software%29 ... scroll to the bottom - "External Links". 

The boxes with arrows pointing elsewhere are perfect examples of what I want to do with a text view, without those "characters" being selectable or editable as characters. I want the text to be fully editable, and unchanged in behavior, however. What's the best way to accomplish this? My target is 10.4 or above, so if there're "more convenient" mechanisms that are 10.4 only, that's perfectly fine.

*Update:* I found the     NSLayoutManager  method     -setShowsInvisibleCharacters:, however this will not work for me for two reasons. One, it only works with fonts which support this function (and there aren't many or are none, according to various posts on the subject). Two, I can't "add" my own (for example, the pretty "external link" image inline with the text). It is suggested here: http://www.cocoabuilder.com/archive/message/cocoa/2004/4/20/104789 that overriding     -drawGlpyhsForGlyphRange:atPoint: and drawing the characters in is the way to go for the standard invisible characters. That's fine -- however I still can't figure out how to "insert" an uneditable, unselectable glyph where I want, such as the external link indicator.

----

**Discussion**

You have complete control over what is selectable and editable in NSTextView.  See the various NSTextView delegate methods.  You also control how text appears.  Apple has an example of how to color and underline URLs embedded in text.  That is a good starting place.  Finally, NSTextView and indeed NSAttributedString provide a mechanism for embedding images in text.  I suspect what you want is a combination of small icons (images) embedded in your text and delegate methods that forbid selection or editing of the icons.

----
I'm familiar with coloring and underlining URLs, etc. That doesn't really seem to answer my question. Are embedded images/icons really the only option available? I can't add a glyph without a corresponding character?
----


NSLayoutManager is responsible for character code to glyph generation, and it is not always a one to one operation.  Some characters are represented with multiple glyphs and sometimes multiple characters are represented as a single glyph.  If you are determined to add glyphs that don't behave like glyphs (not selectable), I suspect you will have to subclass NSLayoutManager.  The much easier solution is to use NSTextAttachementCell.  You could even create a special subclass that draws your "external link" glyph.  What exactly is your objection to using the built in capabilities of NSAttributedString ?

