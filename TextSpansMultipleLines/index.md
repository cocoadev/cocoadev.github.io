---
layout: page
title: TextSpansMultipleLines
---



How can I reliably determine whether a substring in an General/NSTextView spans multiple lines and how many lines it actually spans?
----
Seems like General/NSLayoutManager might be able to help you...check the docs (I don't really know myself).

*Yes, I understand that, but *how*? ;-) The method "rectArrayForGlyphRange:withinSelectedGlyphRange:inTextContainer:rectCount" looks promising because I'm thinking if it returns more than one rect for a contiguous substring, then it's on more than one lines. Does that seem reasonable? -- No, just re-read the description: "The number of rectangles returned isn�t necessarily the number of lines enclosing the specified range. Contiguous lines can share an enclosing rectangle, and lines broken into several fragments have a separate enclosing rectangle for each fragment."*

----

The 'Counting lines of wrapped text' code fragment at [http://developer.apple.com/documentation/Cocoa/Conceptual/General/TextLayout/Tasks/General/CountLines.html] looks like it might have the answer, but I'm not caffeinated enough yet to work out how.

*This is a start, but this method causes all glyphs in the entire container to be drawn. Since I want to call this process every time the text is changed (each character typed, etc.) to draw some pretty effects for user feedback, this is probably not efficient enough.*

You could do it similar to how you would General/ImplementSyntaxHighlighting - maintain a line range where the user is editing and restrict your search to there. The above link says:


"To obtain the number of lines in a single page (which is modeled by a text container), you would use the General/NSLayoutManager method glyphRangeForTextContainer: and restrict the line-counting for loop to that range, rather than the {0, numberOfGlyphs} range, which includes all of the text."


You could just substitute the line range (or paragraph range) for the General/NSTextStorage's     editedRange instead.

----

You could always get the text container's width, measure your line, and if the line is wider than the container, it's wrapped. But there's probably a better way.

*I like this idea. Thanks! I'll try it out and let you know how it works.*

----

Okay, I think I'm asking the wrong question here ... What I'd like to do is to draw a pretty effect around specific keywords - every instance found within the container. I want the effects to be visible while scrolling. I want them to appear immediately as the user types (if a keyword is "foo", as soon as the user's done typing "foo", I want it marked as well). I'm aware of setTemporaryAttribute, but I'd like to use a shiny bar graphic over the text. I thought I was done when I set it up to use boundingRectForGlyphRange: but this doesn't work right if the "key word" to be highlighted is wrapped due to white space ("foo bar" as the keyword string) results in one huge rectangle encompassing both words (and anything else). So I need to apply the effect separately to any wrapped instances of my keyword string.

Make sense? Do I need to provide more info? ;-)

*Well this is essentially a specialized syntax highlighter. Most highlighters don't draw rectangles, though, so whether a line is wrapped isn't superimportant. I don't suppose you can just add 'bar' to your list of keywords?*

I've been thinking along those lines. I'd like to 'highlight' it and add a little lightning bolt image to the end (whichever the 'end' is in whichever language the user is using). Basically, we're talking about users who want to link key words or phrases to General/URLs automatically as they're encountered during a publication process. The linking can be to other special objects as well, so it won't automatically create a standard hyperlink attribute. So I don't want to limit users by saying "enter one word at a time" when a phrase will be needed more often than not in this special case.

----

Ummm...I'm a newcomer to text drawing and stuff -- having my own problems with General/NSLayoutManager. But couldn't you use the text attributes of General/NSAttributedString to draw a background color? That doesn't help with the lightning bolt, though. --General/JediKnil

*Well, yes, but this wasn't what I was trying to do. ;-)*

----

It might be what you have to settle for though :P Unless you can work out a way to just keep keywords from wrapping...

*Settle? Never! ;-) Actually, I decided to emphasize keywords by combining two easily-manipulated capabilities. First, adding a temporary underline trait with a color to match a graphic. Next, draw the graphic at the *end* of the keyword string, 'connecting it' to the end of the underline. It works great at many sizes by just adjusting the y offset a bit. Wrapped keyword strings look just fine using this approach. Especially with a very small indicator graphic (ditched the lightning bolt idea and went with a basic shape - a sphere). Visually, it's very pleasing and a lot less obtrusive. It's also lightning-fast (lightning pun intended) even with a lot of text with lots of keywords. So I guess that's the best I can do for now.*

----

Sounds pretty good.  Where can we see this code running? :-)

*It's little more than using General/NSLayoutManager's addTemporaryAttributes:forCharacterRange: for all found instances of the keyword strings (from an array of keyword strings). Then General/NSLayoutManager's boundingRectForGlyphRange:inTextContainer: to find the *last* character in each keyword string instance and General/NSImage's compositeToPoint to draw the image (with an origin adjusted to your tastes). Call this contraption each time the textview is resized or the text is changed and you're *basically* all set. Of course, I've had to make adjustments for the insertion cursor being drawn over top my pretty image (cutting it up), so it won't try to draw when the cursor is there, etc. It looks and acts alright to all but the most dedicated nit pickers ...*
