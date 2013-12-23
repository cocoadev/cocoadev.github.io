---
layout: page
title: NSLayoutManagerAndHiddenText
---

Hey all-

Im writting a text editor of sorts that needs a certain functionality and I havent been able to figure out how to do it from the docs. The text editor will use RTF and RTFD files, though I don't think the file format will be an issue.  Id like to periodically (say every 5 seconds) write a timestamp into the text file that Im editing.  Id like these timestamps to be written into the text file, but Id like to tag them in such a way that they wont be rendered into the TextView.   The tagging would just a be the timestamp wrapped in some special characters to identify it as a timestamp.  Should I use an NSLayoutManager to fish out the tags and hide them?  Should I use a separate TextStorage for the text without the tags?  Suggestions are much appreciated. 

mat.
----
Well, AFAIK, the files that your program would use wouldn't actually be RTFs, and would be usable by only your program if you wanted the timestamps to be hidden.

If you don't care about this, then what you might do is just add the timestamps to the end of the file, read the file up to the point of the original text, then read the rest of the file as the timestamps and process it that way. This could be done simply with NSScanner and associated objects...

Hope that helps.

----
So more generally, is there a way to get part of the text-system to hide parts of text before rendering it into a view?  Forget about the file format for a second.  Lets say in a certain text file loaded into a TextStorage that I want every @ character be hidden when I render it into my apps view.  Is there a better way to do this than traversing the entire text storage string looking for those @'s and removing them?  I guess I could just keep two NSTextStorage's around,one with the original file and the other with a sanitized file.  Got any better ideas?

