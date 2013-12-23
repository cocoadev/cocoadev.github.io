---
layout: page
title: CustomFocusRing
---

I am fairly new to Cocoa and any help will be geatly appreciated.

I am trying to create a custom focus ring (kinda like Address Book) around the NSTextField but can't figure out. I have a subclass where I have overriden becomeFirstResponder:, resignFirstResponder: and drawRect:. But nothing seems to work. I am sending setNeedsDisplayInRect: to properly invalidate the required area but no help. The area that gets drawn is the bounds of the text field.

Please can someone give me some sample code that would demonstrates how to do stuff once a field is about to become first responder. Not only do I want to drawn a focus ring, I also want to be able to open a drawer as soon as the field is first responder.

Thanks.

