---
layout: page
title: NSTextViewDelegateNotWorking
---



I've been trying to implement autocomplete in an NSTextView (I'm making a source code editor).

I set the NSTextView's delegate outlet to my controller class, and implemented the method:

    - (NSArray *)textView:(NSTextView *)textView completions:(NSArray *)words forPartialWordRange:(NSRange)charRange indexOfSelectedItem:(int *)index

However, I've determined through debugging that that method isn't even getting called. Why is this? Is there some other setup you have to do to implement autocomplete?

--OwenYamauchi

----

Owen, to my knowledge this method is only called with the user specifically requests auto complete (by pressing Option-Escape) in a default setup. Assuming your delegate outlet *is* in fact set correctly, this method is only called with the user calls it.


----

The other setup you have to do is have a NSTimer that gets reset after every keystroke for the autocomplete delay, and call your completion code from there. Or you could use DelayedMessaging - that     blahBlahPerformSelector:afterDelay: method i'm always forgetting the name to

