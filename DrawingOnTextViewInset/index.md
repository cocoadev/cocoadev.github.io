---
layout: page
title: DrawingOnTextViewInset
---

I have a subclass of NSTextView.  I'd like it to basically draw its own margins so that the user can see those margins, but I'd also like to draw on those margins.  Currently, when I call [self setTextContainerInset] it works just fine, but any drawing I do (even after I call [super drawRect]) doesn't show up and the inset simply remains the original color.  Is there any way to draw on the inset, or a better way to impose margins on the text view so that I can draw in them?

----

Read the documentation more carefully. You want NSTextView's -drawViewBackgroundInRect: method.

----

That works like a charm.  Thank you.

So what about mouseDown on the inset?  I don't seem to be getting mouse events for clicks when they happen on the inset.

----

Half a second's searching produced this promising lead: http://www.cocoabuilder.com/archive/message/cocoa/2004/1/26/99609

----

Thank you for the help as half a day searching with NSTextView Mouse Event in Inset etc yielded nothing.  I'm still new at this.  I'll update this with the solution when I find it.

----

At the Leopard Tech Talk I asked about doing this and was told it would be best to have seperate child views to accomplish what I'm doing.  Then the mouse events end up in the right place by themselves.

----

I have been working on a feature for Audipad (http://audipad.com/) which allows the user to click on words in a textview and have the word highlighted. It is mostly working but there are some redraw issues in Tiger. The code isn't very well commented, but you can kind of see what I'm doing in terms of the mouseDown stuff. Not sure if this is what you were looking for, but perhaps it will help. -ZacWhite

http://svn.audipad.com/filedetails.php?repname=Audipad&path=%2Fbranches%2Faudipad-ou%2FAudipad%2FSource%2FCDHAnnotatedTextView.m

I just realized that you are talking about the TextView inset, so this probably won't really be a help to you. Sorry! -ZacWhite

