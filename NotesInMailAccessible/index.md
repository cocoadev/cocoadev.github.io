---
layout: page
title: NotesInMailAccessible
---

Are the Notes in Mail.app in Leopard accessible like the to-dos via the calendar store? Or some other framework?

----

I'm also interested in this - there must be some way to interact them, I mean, why make to-dos accessible and not the Notes?

----

To Dos are part of CalendarStore.framework, which is mainly used by iCal and which Mail just hooks into. I don't think it has the ability to store notes, and I believe that Mail.app stores them in its own store like messages, which would make them inaccessible through normal means. The Scripting Bridge may have something that could interact with them, although I doubt it would be as powerful as an actual framework specifically for Notes. --LoganCollins

----

Check to see if there's a SyncServices schema for Notes.  If there is such a schema, then you can write an app that uses it and syncs its own note database automatically with the Mail.app notes database.

