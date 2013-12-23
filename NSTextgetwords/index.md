---
layout: page
title: NSTextgetwords
---

I am trying to get the individual words of a string to do a find operation on, i have tried using componentsSeparatedByString: but that seems very limited.  if anyone has any suggestions, they would be much appreciated.  thanks

----

Searching the documentation for "words" took about two seconds to show that not only does NSSpellChecker have a nice, easy-to-use function that does exactly what you want (in any language available to the OS), but so does NSTextStorage.

