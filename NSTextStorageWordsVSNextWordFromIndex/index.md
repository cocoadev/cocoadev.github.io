---
layout: page
title: NSTextStorageWordsVSNextWordFromIndex
---



Hi guys,

I try to count words and have problems in string like this:     1. 2. 3. 4. 5.

If I count words using     myTextStorage words] count] it returns 1 (one) word, but if I count words using     nextWordFromIndex:, it returns 11 (eleven). And this problem exists only with numbers and period (or comma after it). If I have each number like x.x (where x is any digit) or string like this     a. b. c. d. e. then it counts correctly and both methods returns 5 words.

(I know I can workaround it by counting words by counting whitespaces between them, but then it doesn't work correctly with japanese language - that language doesn't have whitespaces between words, but my method (    nextWordFromIndex:) works fine with any language except the case with numbers). So is there any workaround or explanation of why     nextWordFromIndex: count a number with period like two words? Can anyone check is it fixed in Tiger? 
(Yes, I'm on Panther still)

----

Personally, I would avoid using [[NSTextStorage's -words method anyway. It is slow, and so no good if you want to implement a live word count, and also it yields strange results - in one test I did, a document with over 40,000 words came up as having only just ove 2,000 word objects in the -words array.

This topic has been helpful to me - I was parsing whitespace to get my word count, but hadn't considered how this would only be suitable for some languages. The trouble is that -nextWordFromIndex:forward: is not, according to the docs, intended for use with statistical analysis such as a word count:

*"This method is intended for moving the insertion
point during editing, not for linguistic analysis or
parsing of text."*

That said, Douglas Davidson, one of Apple's text gurus, actually recommended the use of this method for precisely this purpose:

http://www.cocoabuilder.com/archive/message/cocoa/2004/6/10/109459

So I guess it must be safe to use. I have therefore taken this route in in my own app now.

To (hopefully) answer your question:

The way I am doing this is as follows:


* I cycle through all my text using nextWordFromIndex:forward.
* I use the index passed in and the index returnd to create the range of the word, and then use NSString's -substringWithRange: to get the word itself.
* Once I've got the word, I use NSString's -stringByTrimmingCharactersFromSet: to get rid of any punctuation and whitespace on either side of the word.
* I then check that what I've got left contains letters or numbers using NSString's -rangeOfCharactersFromSet: with alphaNumericCharacterSet.
* If the range returned is not NSNotFound, I know that I have a valid word, and add it to my count.


This way, punctuation and whitespace doesn't get counted.

For a live word count, you can use NSTextView's -shouldChangeTextInRange:replacementString: and -didChangeText: to calculate the difference in word count for the changed text - that way you can have a live word count that doesn't slow down typing on even very large documents.

Hope that is of some help.
Cheers,
KB

----

Thanks for input! I did use     shouldChangeTextInRange:replacementString: and     -didChangeText: but had some problems, so I would not recommend this way. It's better to calculate everything just in     shouldChangeTextInRange:replacementString:

----

After posting on Cocoa-dev, Aki Inoue at Apple recommended using -doubleClickAtIndex: instead of -nextWordFromIndex:, so I am now using that for the actual word count (though I use -nextWordFromIndex: still for my -wordCountForCharRange: method, as it is easier to prevent that method going out of bounds). Personally I never had any problems using both -shouldChangeTextInRange:replacementString: and -didChangeText: for my live word count - this way I can get the range of affected words before and after the change, which requires replacementString being in the context of the rest of the text. Incidentally, you need to support -shouldChangeTextInRanges:replacementStrings: under Tiger.
Cheers,
KB

*Thanks KB!*
----
What about using this:

    
NSArray *words= [myString componentsSeparatedByString:@" "];
int count = [words count];


*This won't work with japanese language.*

----

I use NSSpellChecker's -countWordsInString:language: method ... it works quite well for me.

