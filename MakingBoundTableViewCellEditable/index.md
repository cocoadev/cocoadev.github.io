---
layout: page
title: MakingBoundTableViewCellEditable
---



I've got an NSArrayController that manages my NSTableView content, however, when i use the NSArrayController add: method, the row that's added is not made editable by default (so the user has to double click). Can I somehow tell a certain cell to become editable when a new row is added?

Thanks in advance!

----

To add this functionality, you're going to have to write some code. I'd recommend using a custom controller object (like AppController or MyDocument), and wire your "Add" button to a method in that object. The custom object will need to communicate with the NSArrayController and the NSTableView, so be sure to create outlets for those two pointers.

    
- (IBAction)add:(id)sender
{
    // Let NSArrayController do its thing...
    [arrayController insert:nil];
    
    // Now edit the first column of the new row in the table view
    int row = [tableView selectedRow];
    if (row != -1) {
        [tableView editColumn:0 row:row withEvent:nil select:YES];
    }
}


Good luck!

- ChrisCampbell

----

That's what I was thinking too, but there's a problem... the NSArrayController doesn't select the newly added row (even though its told to do so!). I think this is because I am using it with SharedUserDefaults to store its contents in the prefs plist... the part that's probably causing the problem is the fact that I have to combine multiple rows into one value to store it in SharedUserDefaults... Does anyone maybe have a working way to save NSTableView contents into SharedUserDefaults using bindings without the loss of the automatic row selection when items are inserted?

----

So far, I haven't come up with an easy way to do this...the solution I've used is to subclass NSArrayController, adding table data source methods that get and set values in NSUserDefaults. It appears to work this way...but it's still a weird solution. --JediKnil

