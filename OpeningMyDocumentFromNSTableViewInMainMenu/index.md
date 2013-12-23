---
layout: page
title: OpeningMyDocumentFromNSTableViewInMainMenu
---

I have an application with an NSTableView that lists the documents ('projects') in the MainMenu.nib. I want the NSTableView to open the relevant MyDocument file when a user double-clicks on a row within the NSTableView. I have the double-click responder, the project arrays, and all the other hard stuff wired and coded..... BUT, for the life of me, I cannot get an instance of MyDocument to open (the save MyDocument file loaded), when the user double-clicks. All of the existing FirstResponder and NSResponder, File Owner documentation refers to wiring this kind of activity to a menu action... which does not apply in this case.

Any ideas? Anybody?

----

Is your double-click responder getting called? (Stick an NSLog in it and see if it comes up in Console.)

----

is the target of your table view nil? what is the doubleAction: set to? it's the same thing as a menu item - it's just sending a message up the responder chain.

*Remember doubleAction: isn't sent if the cell is editable.*

