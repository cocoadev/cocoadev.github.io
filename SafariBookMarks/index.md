---
layout: page
title: SafariBookMarks
---

I need my app to get a list of all bookmarks from Safari, does anyone know how Safari stores bookmarks, and how to extract that data?

----

~/Library/Safari/Bookmarks.plist

And it's stored as a dictionary, so just create a dictionary using initWithContentsOfFile: with that path.

----

If you're looking to accept URL and bookmark drags from Safari, the pasteboard type is     BookmarkDictionaryListPboardType. Safari also puts a     WebURLsWithTitles type on there. (I think that's the name... I'll check when I get home tonight.) PasteboardPeeker is indispensable at times.

Here's an ML thread on the subject: [http://www.cocoabuilder.com/archive/message/cocoa/2004/2/28/100240]

----

My code imports the plist into an NSDictionary just fine... but even simple things seem to give me strange problems, like when I do "allKeys" or "allValues" on the dictionary I get back arrays with nothing in them. 

I need a simple way to traverse the entire plist looking for URLs.  I notice that Apple denotes an url in the plist with the identifier "URLString", perhaps a way I can just go from one entry to the next looking for the value for a key of URLString? I thought this would be simple, but it appears not.

In the end, all I want is a long list of every URL the person has in the bookmarks file, comma separated - nothing else at all.

----

It's a recursion problem (with a recursive solution). ;-) In the dictionary you've loaded, you have an array with a key called "Children". Walk through that array asking each item if it has a key called URLString (operating on it however you want if it exists). Also ask it if it has any members named Children - ask them if they have URLStrings or more Children, then ask their children if they exist, etc. This method keeps calling itself on each 'child' it finds, so no matter how many children each item has, it'll keep tracing down, doing its thing and exiting back up to the 'copy' that called it, until it gets back up to the top level adn moves on to the next item at the top. It's a bit difficult to explain ... (for me, at least) ;-) Look up recursion if you don't understand what I mean from this description - anybody have any good URLs for an explanation of creating a simple recursion method in ObjC?

----

*...but even simple things seem to give me strange problems, like when I do     allKey or     allValue on the dictionary I get back arrays with nothing in them*

Now, are you sure that you got back arrays with nothing in them, or did you get back nil objects?  I would guess that you gave it an invalid path, and received a nil object (signifying failure) back as your dictionary.  In turn, messages to the nil object always return nil.  Also,     nil == 0, so     0 == dictionary allKeys] count] could be a sign that the dictionary is nil.

----

Just a guess: tilde is not expanded in the argument to     -[[[NSDictionary initWithPath:].

(XML knowledge should be irrelevant.  XML happens to be the native storage format for an NSDictionary.  Other than that it doesn't matter for this task.)

