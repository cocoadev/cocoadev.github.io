---
layout: page
title: JavaScriptEquivalentOfSearchWithRegexAndReplaceUsingFunction
---

I'd like to discuss and come up with a solution to the equivalent of the following JavaScript code in Objective-C/Cocoa:

text = text.replace(/[^\x00-\xFF]/g,  function(s) {
			if (s in someHash) { s = someHash[s]; }
			return s;
			});

While it is somewhat straightforward to find a Regex library that can replace strings based on a regular expression, it is somewhat more difficult to call a function for each match in order to get a result based on that match.

I'm am curious to see what people will suggest. So far, I can only fathom doing this using some kind of loop with a Regex library and inside the loop, send message/call function that will do a string replace. If the string length changes, the loop that accesses the string will need to be adjusted.

The resulting code must be able to handle multi-byte Unicode characters.

