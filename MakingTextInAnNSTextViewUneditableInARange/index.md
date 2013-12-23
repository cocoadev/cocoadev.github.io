---
layout: page
title: MakingTextInAnNSTextViewUneditableInARange
---

I'm looking for a way to basically call setEditable:NO on an NSTextView for a specific range of text.  I'm writing a music score editor and using the text view to render the music, and I don't want them to be able to delete the Treble Clef, key signature, etc.  I'm working in a subclass of NSTextView of course.

So, any ideas how to basically implement setEditable:range: ?  Any other solutions?

----

Well, I must admit I don't know much about score editors, but my first instinct would be that it's not a natural fit for a subclass of NSTextView. It seems to me that while there are some similarities between a musical score and text layout, there aren't enough... you'll end up having to reimplement so many methods that you might as well have started with a clean music-oriented architecture in the first place, whereby you can lock down ranges of elements by design. I realise this isn't a helpful answer, but it might be food for thought. --GrahamCox

----

I had started with simply a subclass of NSView.  Essentially what coaxed me over to using a text view was the fact that I'm already drawing the notes with fonts, and that I needed selection, cut, copy, paste, etc, and that was largely already done for me with NSTextView.

The solution I'm currently using is the same way I'm drawing the staff lines.  Just using the cocoa text system by hand and painting the glyphs where I want them using drawViewBackgroundInRect.  I've set an inset on the text view so it essentially doesn't interfere with the key signature, treble cleff, etc.  Works just fine.

Thanks for the reply. :-)

----

It's not immediately obvious from the documentation, but there are *many* threads in the cocoa-dev mailing list with the same question. The answer is NSTextView's -textView:willChangeSelectionFromCharacterRange:toCharacterRange: delegate method.

