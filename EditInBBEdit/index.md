---
layout: page
title: EditInBBEdit
---

You might have seen some applications that have an "Edit in BBEdit" menu option, where a file off a ftp server is sent to BBEdit for editing, and then when saved it is uploaded to its originating spot.

This is done using the ODB Editor Suite - "an Apple Event-based interface that developers may take advantage of in order to provide better integration between their products and BBEdit"

Here are some public domain classes that you may use to integrate this functionality into your cocoa application: http://gusmueller.com/odb/

----

I was just starting to think about implementing this (and dreading it), then this link shows up on RecentChanges.. THANKS!

----

Is there any public classes that does the client part? I.e. if my program needs to work with files retrieved from ftp, and wants to use an external ftp application for this.

----
Here is some additional information on the subject: http://www.codingmonkeys.de/techpubs/externaleditor/

----
Transmit uses some other facility for that: you can use Emacs or TextEdit (or, I suppose, anything else) as an external editor, and neither (as far as I know) support ODB. Does anybody know how they do that?

----

Most likely they just watch the file using kqueue. See man kqueue for more info � basically it will notify you whenever someone writes to the file (or you can setup other notifications).

