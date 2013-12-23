---
layout: page
title: NSKeyedArchiver
---

NSKeyedArchiver is a subclass of NSCoder that is useful for encoding and storing objects, such as in document files.

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSKeyedArchiver_Class/

http://developer.apple.com/documentation/Cocoa/Conceptual/Archiving/Archiving.html

The below discussion is more about NSUserDefaults than NSKeyedArchiver

----

I am trying to store an array with paths in the user defaults. I have read about the NSKeyedArchiver, but I am not sure how to implement it in order to save the array values and get them back on application startup. Can anyone help?

----

You don't need the archiver for saving in the defaults. Just save the array object directly, something like this (code off top of head, untested):

    

NSUserDefaults* prefs = [NSUserDefaults standardUserDefaults];

[prefs setObject:myArrayOfPaths forKey:@"myArrayPrefsKey"];



And retrieve the array like this:

    

NSUserDefaults* prefs = [NSUserDefaults standardUserDefaults];

NSArray* myArrayOfPaths = [prefs objectForKey:@"myArrayPrefsKey"];

// you'll probably want a mutable copy here - if not, you need to also retain the array.



Hope that helps! -GrahamCox

