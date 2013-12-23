---
layout: page
title: DiffWrapper
---



I'm interested in creating a wrapper around the Unix     diff command to be able to display the difference between text in two separate NSTextView instances. I want to get meaningful output such that each change gives a range and the type of change (add/del/change) in an easily-parsed format so my own GUI can display these results in its own way. I'm not creating 'yet-another-file-merge' app; this will just be an extra feature of a current text app. In other words, I don't need any of the merging capabilities of the diffutils package. Also, I don't want to diff the contents of files, but rather strings - is this possible with the command-line? I realize I'll have to wrap it using     NSTask and     NSPipe, but I don't believe I saw anything in the     diff man page that shows how to pipe input (since it's two different 'files').

Does anybody have any experience using     diff in a Cocoa application? Would you care to share any insights?

----

You'll have to write your text out to two temporary files and then run     diff.

There is a BSD-licensed implementation of     diff as part of OpenBSD (and mabye the other BSDs) which you could simply incorporate directly into your application, modifying it however you need. I don't have a link, unfortunately.

----

Thanks, I was afraid of that. :-) Doesn't make for very fast switching between documents, unfortunately. At any rate, Google is proving frustrating for finding examples. I see that the     --LTYPE-line-format=LFMT seems to let you specify the output format, but every Google search result points to dozens of copies of the man pages (what the hell use is *that*?) ... so NOW I'm just looking for examples of how I can format the results results so they're easy to parse with NSScanner.

----

If you are willing to invest a bit of time to learn about pipes, FIFOs and select (while Foundation has wrappers, I'd recommend reading one of 
Stevens Books (see http://www.kohala.com/start/#books), you could create two FIFOs where your program is writing the data to 
and run diff with those FIFOs as arguments. But I'd first look into integrating a diff source which is licensed accordingly.

*As long as this wouldn't cause any licensing 'difficulties' for my shareware app, I think the investment in time would be worth it. Thanks for the suggestions!*

BSD-licensed software only requires attribution, e.g. "Portions of this software copyright Some Project", with no prohibitions against selling the product, or anything forcing you to give away the code. Read the license, it's pretty short (about two paragraphs) and easy to understand.

