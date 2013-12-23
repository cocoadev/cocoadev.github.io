---
layout: page
title: NSTableViewAutoSave
---



When I change the size or order of my columns in my NSTableView, it gets AutoSaved in the plist.  However, when I programatically remove a column, it shows back up upon Application restart.  Here's my removal code:

    [tableView removeTableColumn:[tableView tableColumnWithIdentifier:@"Name"]];

Can I fix this?

----

If AutoSave doesn't do this for you, you might store the table columns array in a separate user defaults key (or in the document if it's a document) and reload it manually (either in the app delegate or in the document load code). -- l0ne aka EmanueleVulcano

----

Have you turned     -setAutosaveTableColumns: on in your table view?

----

Yeah, I know that it is on, because resizing of columns still saves.  Just removal or addition isnt stored.

In my plist file, there is a     NSTableView Sort Ordering table array, but it looks like this only works for the order of the columns, not which exist and which dont.  Is there a seperate place to set this type of Autosave?

----

I'm having the same problem. I've tried lots of things, for example this: http://www.cocoabuilder.com/archive/message/cocoa/2004/4/7/103584

But no success. Keeping a separate array could probably be one "solution" but that's just seems like an awful waste of code. This should just work, sigh...

