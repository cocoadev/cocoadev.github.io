---
layout: page
title: ReplaceCharactersInAttributedString
---

What I need to do is take an NSAttributedString, search it for a specific set of characters, say an @ symbol, and then replace that symbol with something else. I looked into NSScanner, but it doesn't seem to work with NSAttributedString. Does anyone have a good idea? This is probably really easy and I'm just overlooking something. Thanks.

*NSMutableAttributedString*

Sorry, that's what I am using, but I need an idea of how to actually search it and replace the characters.

**Pull out its string using the     -string method. I don't really blame you for missing this; it seems like an attributed string should just work like a string sometimes. NSMutableAttributedString also has a method     -mutableString, which returns an NSMutableString that tracks the changes to the attributed string. --JediKnil**

