---
layout: page
title: WhereToFindRealDictionaries
---

I'm looking for dictionaries to transform them into General/NSDictionary or General/NSArray objects. If they can be found already formatted, it's even better. Does anyone know where I can find this ? I want to make a prog that can verify if a certain word exists. Also, if possible, a french list of words. In fact, as many languages as possible...

-- Trax

----

How about [http://wordlist.sourceforge.net/]? And I'm assuming you know about /usr/share/dict/words

----

The Cocoa General/NSSpellChecker class will let you check for the existence of a word.  See the methods     checkSpellingOfString:startingAt: and     checkSpellingOfString:startingAt:language:wrap:inSpellDocumentWithTag:wordCount: in http://developer.apple.com/documentation/Cocoa/Reference/General/ApplicationKit/ObjC_classic/Classes/General/NSSpellChecker.html

--General/PhilippeMougin

----

Those are good ideas, but I actually need the list itself because I want to sort the words in different ways. There must be dictionaries for different languages somewhere in Mac OS X, but where and how to extract them ?

----

I would use a text editor like General/BBEdit to do a find and replace on the wordlist file of your choice and format it in plist form, then use     - General/[NSString propertyList] or     + General/[NSArray arrayWithContentsOfFile:] to convert it to an object of your choice. --General/RobinHP

----

I think dict.org might have what you are looking for.  If you visit the links page http://www.dict.org/links.html you will find a list to ftp servers such as ftp://ftp.dict.org/pub/dict/ where you can download the Pre-formatted databases, their in the pre directory.  You will find General/WordNet and the Webster's Revised Unabridged Dictionary (1913) among many others.  You can also scroll down to the DICT Databases section of the page and download the raw data directly from the source of the dictionaries. If you visit http://www.freedict.de/ you will find a collection of free bilingual dictionaries.

--General/EdwardHillenbrand

*The freedict.de only has binary data for a particular - wie sagt man? - engine - but I found a nice German-English text set at*

http://dict.tu-chemnitz.de/

----

dict.org seems great, but alas, I can't get access to their ftp server. And all the other alternatives are in English only. Is there any way to access to the 35 dictionaries contained in Mac OS X ? (I say 35 because Mac OS X is supposed to be in 35 languages) ... I tried /usr/share/dict/words, but it seemed to be only English and I didn't know what to do next. I'm not a Terminal guru...

----

The build in spellchecker dictionaries are at /System/Library/Services/General/AppleSpell.service/Contents/Resources/ but they're binary files.

----

If you�re going to be working with dictionaries directly, consider using a purpose-specific data structure like a trie rather than a standard collection.
