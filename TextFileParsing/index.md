---
layout: page
title: TextFileParsing
---

I have a simple txt file that contains a word list. There's something like 350,000 words in the file, each separated by a line break.
I want to perform database style search in that word list.
What kind of object would be the most efficient for that kind of task ?
And how do I parse the txt file so I can put all the words in a workable array ?

I will be searching the file more than once during the runtime of the app.
I don't think I will add words into the list for now, but maybe this will be implemented sometime.
When I search, it will be entire words and substrings.
Lastly, how does a text file identify its character encoding ?

----

What do you mean by "database style"?  Do you add words into the file or is it static? 

If you just want it in an array, the easiest thing you could do would be this:
    
array = General/[[NSString stringWithContentsOfFile:*filepath*] componentsSeparatedByString:@"\n"];


One thing I'd be careful of:  Even a simple text file should identify what character encoding it uses.

If it starts with a byte order mark, the system will treat it as UTF-16 or UTF-8.  If you save a text file in General/TextEdit or something like that this will automatically be the case.  See http://en.wikipedia.org/wiki/Byte_Order_Mark if you're interested, or maybe http://www.joelonsoftware.com/articles/Unicode.html .

----

On the data structures end of things, a sorted array would let you do a binary search, which is fast and can handle incremental searches. You can load all of your words into a regular General/NSArray and then sort it, write a binary search that uses it, and you're done.

On another note, parsing text files with General/NSScanner and the like is really slow. By all means, implement it in the easiest way possible at first, but if it ends up taking longer than you want, you should seriously consider loading the file into an General/NSData, parsing it using standard C string functions, and only turning them into General/NSStrings at the last moment.

----

On the data structures end of things, a sorted array would let you do a binary search, which is fast and can handle incremental searches. You can load all of your words into a regular General/NSArray and then sort it, write a binary search that uses it, and you're done.

Additionally, the standard C-string functions are not character encoding savvy.  I think that's required in practice as well is in theory here, since ASCII "\n" might share a byte with a multibyte unicode character.

If it's UTF-8, then \n is \n. Most other encodings are also ASCII-compatible, where if it looks like ASCII then it really is ASCII, by making sure the high bit is always set for non-ASCII data. That's not certain, but UTF-8 is certain to work perfectly fine searching for ASCII characters with the standard C-string functions.

----
And why do you think it's UTF-8?  That isn't a safe assumption.  The bottom line, especially for beginning programmers, is that you cannot afford to be naive about character encodings.  The days of assuming everything's ascii, or that you only need to handle ascii, are dead and gone.

I like Joel Spolsky's "The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets (No Excuses!)" http://www.joelonsoftware.com/articles/Unicode.html

It isn't like it's hard to do or anything, all we have to do is use General/NSString instead of C-string stuff.
**
----

Wouldn't General/NSSet be better than General/NSArray for searching?

----

Look at Boyer-Moore Algoritm.  This search method is fast for partial word searches.  I think that in the search field that there should be a setting for full word and partial word.  Full word search would be much faster since you can use the binary search. Partial would be slower.  Here is an ok link on the subject: http://www-igm.univ-mlv.fr/~lecroq/string/node14.html

*
Depending on what's going on, it can be pretty bad UI to have something like a checkbox for "full word search".  You're basically exposing details of the implementation to the user, who is going to be confused why you even want to know whether or not the substring happens to be a full word.  UI generally trumps efficiency in cases like this.  Think about how annoying it would be if General/XCode wanted you to specify.

Also, I don't see much point in using a binary search for his full word searches.  An General/NSSet would be faster, barely more expensive in memory, and dead easy to use.

There are still lots of other interesting algorithms.  Take a look at suffix trees and suffix arrays.  Much faster than Boyer-Moore for a static string to search against repeatedly, like in this case.

http://www2.toki.or.id/book/General/AlgDesignManual/BOOK/BOOK3/NODE131.HTM
*
----

For now, I just use an General/NSArray to query the items. The results are not slow enough to be considered an issue, but I think it could need speed as the implementation evolves. I have something like a "wild-card" search. The user inputs letters, and asterix (*) that can be replaced with any character. With 10-letter words and up, it takes some time. I could try with General/NSSet and see if there's a significant speed change. I also thought about scanning through the word list as a General/NSData
