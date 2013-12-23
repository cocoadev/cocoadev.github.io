---
layout: page
title: InsertAtmarkInString
---

I'm creating a wrapper for the ftp tool provided with MacOS X 10.3 (just to teach me some more basics).

I want to call the ftp application using one string, like: ftp://user:password@url
Only problem is I don't seem to be able to insert the "@" using stringWihFormat. My stringWithFormat call looks like this:
 
args = [NSString stringWithFormat:"ftp://%@:%@\@%@", [login stringValue], [password stringValue], [url stringValue]];

I think there could be a way to insert the "@" using CFString, but I'm not sure. Can someone help me on this one?

Wolfgang
----
You don't need to do anything, since @ has no special meaning, except when preceded by a % (in the same way that you don't need to escape the letter d just because %d has some special meaning). Remove the backslash and you should be ok.

----
Thanks for your help. You were indeed right, my code wasn't working because I forgot a ';' somewhere.

