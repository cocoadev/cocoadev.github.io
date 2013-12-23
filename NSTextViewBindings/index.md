---
layout: page
title: NSTextViewBindings
---

How come NSTextView doesn't have bindings options for font family and font size etc? Is there away to set this up using bindings? NSTextField has the bindings options I want but I must use a NSTextView. Any ideas?

----

Let me state this a little more clearly. ;-) Since the bindings you mention control all of the text in the text view, you must turn off "Multiple Fonts Allowed" in Interface Builder for that text view. Having this option on means the user can use the font menu (or a variety of other methods to insert text with varying fonts. Setting the font on the entire text view would wipe this out and homogenize the text according to this new setting. With multiple fonts allowed, those bindings don't really make sense because they'd override whatever font changes the user attempted to make, which of course is conflict. Bad programmer! ;-)

The solution: Turn OFF multiple fonts allowed on the text view in question and the bindings will appear. Remember to double-click the text view because single-clicking it selects the enclosing scroll view, exposing the scroll view's bindings instead.

----

I've turned off multiple fonts and the user can still use multiple fonts, plus the bindings stuff now works. This is ideal :)

