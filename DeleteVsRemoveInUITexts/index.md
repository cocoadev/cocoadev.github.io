---
layout: page
title: DeleteVsRemoveInUITexts
---

Is there any rules on when to use remove instead of delete? Login Items and Mail preferences use 'Remove' when an item can be removed from a list, but e.g. Project Builder uses 'Delete' for what to me seems like the same thing.

If I were to formulate a rule myself, it would be that 'delete' will not only remove the item from the list, but the item will disappear, i.e. one cannot just add it again (as is the case with e.g. Login Items), but e.g. in Mail it uses remove about removing filter rules and accounts, which are lost, whereas Project Builder uses Delete for e.g. Bundle Resources, which are not deleted, but simply removed from the inclusion list.

To add to the confusion, the Edit menu always has a 'Delete' menu item, which I would say should apply to the first responder, which could be a table view (although no-one really seems to do this).

----

Maybe it's to do with how user-friendly the app is supposed to be. "Delete" sounds much scarier...

----

Whatever you do, do not look too hard at Project Builder. PB is geared towards experts and not really an example of good UI.

This is what the AquaHumanInterfaceGuidelines has to say about the delete menu item:

* Delete: Removes selected data without storing the selection on the Clipboard.  Choosing Delete is the equivalent of pressing the Delete key or the Clear key.*

----

Favor using the more common, friendlier words.  Remove vs Delete, Stop vs. Abort, etc.

----

Actually, it would seem that AddressBook, iCal, Safari (bookmarks), and the System Preferences 'Accounts' all use Delete (this all about operations which loses the data, and does not just remove it from a listview, like e.g. the Login Items).

----

Seems to me, that perhaps Delete should remove an entry from an application, so that it is not accessible without re-importing it, while remove might erase an entry from a list, but the entry exists somewhere else in the application, so it can be accessed and re-added without explicitly using the file system (open/save dialogs). Just my $0.02.32 CDN.

----

The pace of inflation almost demands that you contribute your 167.39 HUF  http://en.wikipedia.org/wiki/Forint

----

Delete is used for files in the file system and for selected text or objects; remove is used for everything else. - JonathanGrynspan

