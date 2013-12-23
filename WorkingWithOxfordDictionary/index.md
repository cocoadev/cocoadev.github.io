---
layout: page
title: WorkingWithOxfordDictionary
---



I am making an application that takes a user inputed list and fetches the definitions to the words given. This process takes quite some time, as I have it going to Dictionary.com via the curl command and parsing that result.

I think it would be much easier just to use the built-in dictionary for this task, yet I can't find a way to do it. Obviously there is, as the Dictionary.app/widget could not function.

Thanks
~Balthamos

----

Typing "Oxford" into Spotlight quickly found the Oxford dictionary package located at /Library/Dictionaries/.  The relevant files are inside the Contents folder of this package.   I dunno what the format is.  Good luck.
*Those files seem to be encrypted. I have checked them out before, but could not make any sense of it.*

Well, *.dict/Contents/dict_body are XML files with a well-defined DTD, while dictionary_* are binary indexes into it, in some proprietary Apple format. You might get away by parsing the XML file if you want, but it's many General/MBs long (16 MB for the thesaurus, 79 MB for the Oxford dictionary).

----

There has to be a way to mimic what Apple has done. Once Dictionary has done a preliminary search, it takes only 1-2 seconds to find words after that. Is that Cocoa somehow interfacing with XML? ~Balth

----

The binary file appears to be a representation of a 'trie', which is a very efficient datastructure for storing data indexed by strings.  You can search google for more information.

----
I'd suggest you use the same approach as Apple: Once, you build a custom datastructure from the huge dictionary xml (almost xml) file, and then use that to access the information.

----

Thanks for the input guys; I'll see if I can't get some code together. It still baffles me that Apple didn't add in an AS action to fetch a definition or synonym. Perhaps they will in later versions. ~Balth

----

I've only glanced at the XML file and so I'm not entirely sure if this would work, but it appears to be sorted. If you aren't afraid to make a lot of assumptions about its structure, then it would be possible to write a binary search routine that works directly on the XML file without any associated index. I've done this with a less-complicated text file. The binary search has to backtrack to find the beginning of an entry before deciding how to cut the search, but it can be made to work if the beginnings are recognizable without a lot of context, and if the entries are sorted. Anyway, I thought I'd toss this idea out there.
