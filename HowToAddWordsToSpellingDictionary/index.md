---
layout: page
title: HowToAddWordsToSpellingDictionary
---

I am wanting to add some words to the system spelling dictionary for my program.  It's an editor for an industry that uses a lot of technical terms, and I want to have those terms not be flagged as misspelled.  But I also want the system to check spelling for the user's language.

Is there a way to tell the spelling system "for my app use these extra thousand term".  I don't want to add them to the dictionary for all applications. The NSSpellChecker method     ignoreWords:inSpellDocumentWithTag: looks promising, but there's no documentation about what tag to use for the document tag, and no way is obvious how to tell an NSTextView what document tag to use for its spelling needs.

Using an NSSpellServer looks like it will replace the current spell server, and I don't want to have to reimplement English spell checking for my app.

Thank you

----

Not of much help, but the tag is obtained by calling the NSSpellChecker class method:     uniqueSpellDocumentTag.

To figure out what tag the NSTextView got, you'd probably need to do MethodSwizzling

----

I've done a poseAsClass: on NSSpellChecker, and NSTextView calls -[NSTextView _checkSpellingForRange:excludingRange:] once it starts spelling words, so no easy way of intercepting that that won't break in the future. I have successfully been able to add words to be ignored by overriding     uniqueSpellDocumentTag in a posed' class and always returning the same value, which lets me use     ignoreWords:inSpellDocumentWithTag:.  That's kind of gross, though.  And it's not case sensitive.  Whine whine whine.

