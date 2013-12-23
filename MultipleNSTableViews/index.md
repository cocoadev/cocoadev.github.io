---
layout: page
title: MultipleNSTableViews
---

What would be the best way to have 2 NSTableView's interact with each other? for instance, when the selection of one changes, the other one reloads to reflect the new selction, kind of like changing a playlist in iTunes. Would 2 datasources be necessary, or is there a better way to accomplish this?

TIA,
-Brandon

----

Use the normal methods that are called on the delegate (    -numberOfRows and     -tableView:objectValueForTableColumn:row) and also the     -tableView:selectionDidChange: notification. In the notification, you can call     -object on the passed notification to get the NSTableView that sent the notification. -KevinWojniak

----

If either table is fairly complicated, having only one table controller for both tables could get very messy. You might be better off using two table view controllers. It is largely a design issue as to how those controllers communicate.

One way which has worked very well for me and exercises MVC is to have a model object which keeps track of the current item. Let's stick with the iTunes example. You could have two table controller classes and a **playlists** model class (which keeps track of all playlists and which one is currently selected). When a different playlist is selected, the table controller tells the **playlists** object which playlist has been selected. The **playlists** object sends a notification out saying the playlist selection has changed. The other table controller listens for this notification and reacts appropriately. If you don't know how notifications work, I would check out Apple's documentation on them. They are very useful when it comes to this kind of thing. -- RyanBates

