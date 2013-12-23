---
layout: page
title: GrabbingTextUnderMouse
---

How would I grab text under a mouse cursor for any application that might be open?  That is, I would want the text to go onto a pasteboard without having to select and and copy.  The cursor (or pointer) would just have to hover over the text, without selecting or any clicking down.  The text would be copied a word at a time.  If the mouse moved, the word in the pasteboard would be wiped out and the next word would replace it.

I have seen something comparable to this in Windows/Delphi, where you drill down to the window's text container and can capture the text by the character depending on the mouse's x and y coordinates within the window.  Is it a similar process in Cocoa?

----

Wouldn't Distributed Objects apply here?

----

I'm thinking the AccessibilityAPI introduced in Jaguar is your best bet.  Apple has some sample code demonstrating how an accessibility client works here: http://developer.apple.com/samplecode/Sample_Code/OS_Utilities/UIElementInspector.htm  -- Bo

