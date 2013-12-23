---
layout: page
title: ExportTextViewtoASCII
---

I am trying to find out how to export the contents of a NSTextView to an ascii format like a txt file. Can anyone show me code that does this or refer me to some page that has this information on it ? Thanks!

----

    [[NSTextView string] writeToFile:@"path/to/file.txt" atomically:YES]

See [http://developer.apple.com/documentation/Cocoa/Conceptual/TextIO/Tasks/UsingTextIO.html]

Also check out NSDocument, which does alot of the work for you.

