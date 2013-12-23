---
layout: page
title: UsingDelegatesForTableViewSelectionDidChange
---

I have a window with two tables on it.  I need to do different things when the user clicks on a line in either table which my General/NSArrayControllers can't handle.  This was easy enough when I only had one table:  in the controller object for that window (in this case it is actually my document acting as the controller--simple app in this case) I overrode the tableViewSelectionDidChange method to do what I needed to happen when a table row was selected.

With two tables, though, this won't work.

How should I implement this?  

I was thinking that I could use two seperate objects created by my controller to act as the seperate delegates and create the tableViewSelectionDidChange method in each (appropriate) one, but the problem I have then is:  How do my delegate objects refer back to various data objects instantiated by the controller object (remember that my document is also my controller) that also instantiated these delegate objects?

Or is there some way in side the tableViewSelectionDidChange method to determine exactly WHICH table caused the notification?  This seems like the easier, more logical approach if possible.

I hope my question is clear enough.

Thanks,

General/KentSignorini

----

tableViewSelectionDidChange receives a Notification parameter.
[notification object] is your General/NSTableView.   
- Bjoern Kriews

----

So if my General/NSTableViews were created as:     tableViewOne and tableViewTwo, could I do this inside tableViewSelectionDidChange?:

    
if ([theNotification object] == tableViewOne) {
    do the first set of stuff
} else if ([theNotification object] == tableViewTwo) {
    do the second set of stuff
}


I just need to determine which one caused the notification.  Will this work?  *Well, what happened when you tried it?*

Thanks,

General/KentSignorini

----

    tableView:shouldSelectRow: not sure if this will work for you, but it might be worth a try...
