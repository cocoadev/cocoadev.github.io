---
layout: page
title: DragAndDropFileToFinder
---

I wrote an address book application that pulls name/phone number information out of a proprietary database and displays it in a nice table view. Currently, dragging a person out of the table on to the desktop generates a text clipping with that persons name, phone, address, etc. What I would like to do is have it create a vCard, using the new createvCard capabilities in the address book APIs. How do I drag something to the finder and have it create a specific kind of file?

Thanks!

See: http://developer.apple.com/techpubs/macosx/ReleaseNotes/AppKit.html - the section entitled "HFS Promises".

