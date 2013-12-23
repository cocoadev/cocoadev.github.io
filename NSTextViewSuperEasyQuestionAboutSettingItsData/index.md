---
layout: page
title: NSTextViewSuperEasyQuestionAboutSettingItsData
---

I have an NSTextView for which I want to:

1)  store its data in an NSData variable

2) set the content of this NSTextView based on the content of some NSData variable

(The idea here is that I want to store the NSTextView's data in a variable for encoding with all my NSDocument's other data before saving and then after loading, replace the NSTextView's data with that which has been decoded after loading.)

I've read NSTextView's help and it's very confusing for me.  Using an object controller it's fairly easy to point to an NSTextView's NSData data object, but I want to do this with straightforward accessor methods for this simple application.

So, what is the accessor method to grab the NSTextView's NSData object?  And what is the method to change its data to the newly loaded NSData object?

Thanks,
Kent!

----

NSTextView is a subclass of NSText. NSText has many of the methods you need to flatten and unflatten rich text format (RTF). Take a look at     replaceCharactersInRange:withRTF: and     RTFFromRange. One method takes an NSData object as an argument and the other method returns an NSData object for a given range. 

----

Thanks!  Worked like a charm!

Kent!

