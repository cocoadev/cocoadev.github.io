---
layout: page
title: DynamicallyResizingTextViewOrField
---



I have been looking to create an NSTextField or NSTextView that dynamically resizes its vertical height based on text entered. An example of this can be seen in iChat's and Adium's message entering view.

So far I have tried using NSTextField's and the size of the attributed string, but of course this has its limitations in that you cannot accurately measure the size of each line in the view. I assume that both iChat and Adium use NSTextView's as it seems to provide the best way of gauging the length of a line of text.

Does anyone have any experience with NSTextView's and dynamically resizing? I have busied myself with the documentation of NSLayoutManager, NSText and NSTextView for the last few hours but I cannot seem to find any way of achieving it. 

Thanks in advance. -- MatPeterson

*There's some code over at TextBox that you might find useful.*

I've posted some code for finding the size of an NSTextView's contents at NSTextViewSizeToFit.

----

Neato, didn't think it was that simple. Once I have the view written up I will post it there. Thanks guys. -- MatPeterson

----

Anyone figure this out for an NSTextField as opposed to an NSTextView? Something like the text boxes in iCal and AddressBook.

*You could probably make it work, but in the end it may be easier to simply use NSTextViews instead. You can set up an NSTextView that looks and acts just like a text field by having its enclosing scroll view draw a focus ring and setting the text view to act like a field editor.*

----

See CCDGrowingTextField :)

----

I just finished making IFVerticallyExpandingTextfield.  I think this is exactly what you're looking for.

----

I just posted some code here:
http://brockerhoff.net/bb/viewtopic.php?p=1982#1982
to find the minimum size for content, for NSTextField; works for any sort of bezel or border, while editing or not. -- RainerBrockerhoff

