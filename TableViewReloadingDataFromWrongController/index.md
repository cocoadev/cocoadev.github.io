---
layout: page
title: TableViewReloadingDataFromWrongController
---

I have used tableviews many times without problems. I have 2 nibs in my current app, mainNib and tableNib. I set up a tableview in a window in tableNib, set the datasource and delegate to the tableNib controller, and the outlet from the contoller in tableNib to the tableview. However, this particular tableview is for some reason trying reload data from mainNib. Is there something I am missing? Are there any connections between the 2 nibs that would cause this? I am completely vexed and would appreciate any ideas.

*Could you explain a little more on how the table view is attempting to reload data from the mainNib? What exactly is happening?*

Your description has a nonsensical point: an General/NSTableView needs an *object* as a data source. It is impossible to use a nib ("mainNib"), which is a file. The only way an object (tableView, what-have-you) can send messages to another object is if you set an outlet explicitly. As for inter-nib communication, that is also not possible (except if you set it up on purpose which you cannot do by accident). Again, nibs are *files*. Files do not communicate, objects do. Nibs contain objects, but they are not working objects until they are woken up, at which point the contained objects only know about those objects which they have been connect to (as outlets or action targets), and possibly the nib file's owner. Possibly you have set an outlet in the nib and then overridden it in code, maybe in an -awakeFromNib method somewhere (likely a file's owner). �General/BrentGulanowski
