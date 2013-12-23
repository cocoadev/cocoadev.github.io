---
layout: page
title: HowToUseFootnotes
---

**The basics**----

Footnotes allow you to create a page-specific form of WordsJammedTogether: give a word a definition so that whenever it is used on that page, it forms a link to the definition. **No other page is affected.**

Footnote words must start with a lowercase letter, and can only contain letters and digits. To define a word, just precede it with \\%\\%DEFINITION\\%\\% (no whitespace between the two). That instance of the word will be unaffected; all other instances will become links to it.

You can also create method definitions this way; any definition with words starting with a lowercase letter, each followed by a semicolon (no whitespace) will be treated as a method, and an algorithm will be applied to try to mark all uses of it in code. This may or may not be successful in any given instance, however, so if you get odd artifacts on a page with method definitions, just stop using them. See KTLLMutableArray for a complex instance of its use (specifically the method call to -initWithArray:firstOffset:forCollection: in -objectEnumerator).

----**Suggested uses for footnotes**----

* Making footnotes on a page (see CocoaMostWanted)
* Making an index on a largish page (see DesignRedBlackTree)
* Defining methods on a class definition page (see KTLLMutableArray)
* Monkeying around in the SandBox
* Organizing the maelstrom of discussion topics on a largish page (see AssemblerCode for a good example of where it might be used).


----**Update (18 October 2005):**----

The footnotes code was a huge ugly mess that nobody used, so I dropped it when I rewrote the CocoaDev parser. -- KritTer

----
I personally do not mind, though sometimes these came in handy. If no one actually wants to bring footnotes/definitions back, we should put this (now outdated) page on DeleteMe. --JediKnil

