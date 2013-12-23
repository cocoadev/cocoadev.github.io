---
layout: page
title: LimitingTextFieldInputToNumbers
---



This seems like it should be a simple thing to find, but I've searched around and I can't seem to find any information.

I have an NSTextField and I want to limit the text input to be numbers.  The only way I can see is to intercept each key event and delete the text if they enter anything other than a number (or similarly use a text changed event to do this).

Is there a nice clean way to do this like putting a filter on the text field to only accept characters from a certain set?  Or am I just having a pipe dream from prior programming work in Java?

----

Solved: See NSNumberFormatter

