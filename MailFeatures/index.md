---
layout: page
title: MailFeatures
---

My question is, in Apple Mail when you select a message ((NSTableRow) -- What is that?)it shows different information in the NSTextField depending on the selected row. How is that done?

--JoshaChapmanDodson

----

    [tableView tableViewSelectionDidChange], NSTableViewSelectionDidChangeNotification
----
Is there any example code for that, because that was confusing 

----

Look in the documentation. Its not our job to write the entire applications for you. NSTableView has a lot of stuff in it and we are not about to describe it all.

Hint: delegate and this:

    

- (void)tableViewSelectionDidChange:(NSNotification *)aNotification



Btw, its not an NSTextField, its more likely an NSTextView or an NSView.
----
Sorry to bug you, but I think I should of said when you select a Mailbox, it shows a list of messages, how is that done?

----

outlineViewSelectionDidChange:, NSOutlineViewSelectionDidChangeNotification

If you don't understand what to do from these pointers, you really need to read the documentation. (I would say reread but it's obvious you haven't yet looked at them.)
----
What documentation?
----

It doesn't seem to be only the documentation he needs to read, but also a book on basic Cocoa paradigms, like delegates and notifications...

- JosephSpiros

