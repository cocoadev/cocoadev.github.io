---
layout: page
title: NSTreeControllerAndNSBrowser
---

I am trying to use NSTreeController with a NSBrowser. Thus far, the NSBrowser is being populated just fine with the proper data from the NSTreeController. The only problem I am having is when I select something in the NSBrowser, my text controls (linked to the NSTreeController/selection) are not being updated. I can remove the NSBrowser and insert a NSOutlineView and when I make a selection there, all works great, controls are updated, etc... But not with a NSBrowser. I can somewhat understand because a NSBrowser has multiple items selected at all times, well most of the time, in different columns.

But I cannot figure out how to handle the situation. Can anyone offer a suggestion or point me in the right direction? As I said, the NSBrowser is being populated correctly but it does not seem to be sending the current selection on to the NSTreeController.

Thanks!

----

Thanks to the guys on irc.freenode.net#macdev ... I found I was not binding selectionIndexPaths in the NSBrowser. Binding that to NSTreeController to selectionIndexPaths. That made all work, exactly how it should.

