---
layout: page
title: EncapsulateAnyDataWithNSData
---

I want to make a simple app to encapsulate anything that is dragged onto its icon and send it to another machine on a LAN - much the way you can send data using iChat.  Everything works fine using NSData if the file I am sending is a text file, but if the object I drag to the icon is a folder (or .app) then  NSData doesn't handle it, at least not with 'dataWithContentsOfFile:' (of course).  Any ideas?  should I make a zip archive of the dragged items and send that?

----

Yes, you'll have to tarball/zip/whatever the contents of a folder. (You could take a look at NSFileWrapper but I don't think it's designed for that).

----
I disagree with the above comment.  You can do a directoryEnumerator and send the files "one at a time".  If you want to reproduce the directory hierarchy, you could also encode the directory structure and send it along as a "special" file that the receiving app would act on.  Tarring is, in my opinion, though, a much better (and simpler) solution.  Although, if any of the files you're sending have resource forks, kiss them goodbye if you're using tar.  I don't think any of the NSData routines will encode resource forks (though don't quote me).  If you use the OS X zip helper you'll be set.

----

yes, sometimes those .app's have hundreds of little resources and sending each one separately may significantly slow down the process.  On the other hand, TARing each one also is a slow process...sigh

----

You can also use     ditto -c  --rsrc [--sequesterRsrc] [-k] to archive things. If the -k option is present output will be a zip archive, otherwise it'll be a gzipped cpio archive.

----

is there anything that encapsulates without compressing?  I wonder how ichat handles this issue...  :-/

----

Tar encapsulates without compressing. But I think the simplest thing to do would just be to have one routine for sending folders and another for sending files. (And as for resource forks � you could just either use hfstar or copy what it does.)

