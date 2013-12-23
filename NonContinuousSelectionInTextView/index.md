---
layout: page
title: NonContinuousSelectionInTextView
---



I want to implement such feature, so when you search for a word, all occurences of this word are selected in a text view. I didn't find anything in Google, but I know it's possible (MacJournal for eg does it when searching). So, how to make non-continuous selection?     setSelectedRange: takes a continuous NSRange.

----

I don't think you want to select the words, so much as just change the background color, yes? Look at NSAttributedString. You can set an attribute for the background color and foreground color.

*MSWord and i think Nisus does this so it is possible.* 

**Yeah, but MSWord definitely isn't Cocoa.** and I don't think the OSX (Cocoa) Nisus has noncontiguous selection. TextMate does it (column selection - same thing as far as we're concerned here), but it doesn't use NSTextView.

*SubEthaEdit does something like this with their option drag.*

"Pages does this when you select text and then command select another piece and i'm pretty sure that's cocoa"

----

Umm... Thanks everybody

*Your quite welcome. Anyways... I think pages does indeed use NSTextView, at least it acts like it.*

*For the record, this is in Tiger and is free.* -cp

Tiger NSTextViews handle noncontinuous selections.  Simply hold the command key while selecting text.  To do this programmatically, look for the "setSelectedRanges:" methods in the documentation for NSTextView.  If you wanted to implement this yourself (for earlier OSes perhaps), your best bet would probably be to listen for a modifier key on mouse down/drag (command seems standard) and record the text range of the drag in an array or set.  You should probably then (or before the next drag) color the background of the selected range using the system highlight color.  It won't technically be selected but it should look like it was to the user.  You also need to override anything that performs changes on the text and rewrite them so that they work through the array of text ranges that you've been saving.  Finally, any click or drag that doesn't have the modifier key held down or other event that would cancel a selection should cause the currently "selected" text ranges to redraw their backgrounds so that they don't look selected anymore and the array to be cleared.  This is about the only way I know to do it using NSTextView.

