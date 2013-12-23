---
layout: page
title: CopyingNSTextStorage
---

How the heck do you do it?  NSTextStorage does not implement its own copy methods!  I tried using copy (came up with an NSAttributedString) and mutableCopy (came up with an NSMutableAttributedString), and even tried using a category to implement it, but to the stickiness of a malloc error (don't ask me how I got it, because I do not do C memory management).  How the blast can you do it (with layout managers and everything)?  Any help is appreciated. -RossDude

----

NSTextStorage is a subclass of NSMutableAttributedString. Unless I'm missing something, all you need to do is create a new text storage and use NSAttributedString's initWithAttributedString: method, passing the old NSTextStorage (since it's  an NSMutableAttributedString with additions).

----

And then call     replaceTextStorage: on your layout manager. Copying NSTextStorage doesn't really make sense, since all it adds to the string is links to other objects.

----

Agreed, but it's always best to answer the question anyway. ;-) Just in the off chance there might be a legit reason or need.

