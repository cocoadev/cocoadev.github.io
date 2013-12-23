---
layout: page
title: NSTableViewByHand
---

I'm trying to create a NSTableView by hand (I need it for a particular case where I can't use a nib) and add it to a window, but it is never displayed. The code I'm using is:

    

// ... window creation ...

table = [[NSTableView alloc] initWithFrame:NSMakeRect(x,y,w,h)];

[table setDataSource:myDataSource];
[table setRowHeight:32];

NSTableColumn *column = [[NSTableColumn alloc] initWithIdentifier:@"myColumn"];

[column setEditable: NO];

[table addTableColumn: column];

window contentView] addSubview:table];

// ...



Any hint?

James

----

Are you *sure* you can't use a nib? Why can't you? This is vastly easier if you use IB.

You will at the very least need to wrap the table view in an [[NSScrollView to get correct behavior.

----

I'll need to create the window and all of its content in execution time (it will be generated from a text file that can be edited). So, the nib won't work here.

----

That means you can't have the *window* in a nib, but it doesn't mean you can't create a nib specially for your table view (and any other difficult-to-create interface items). Of course, if the text file specifies everything about the table view then this may not be too useful.

Are you sure you're specifying the proper frame rectangle in your code?

----

Thanks for reminding me about the scrollView! Now, at least I can see a white rectangle on the window, but still no tableView... and yes, apparently the rect info is fine (it's the same code I used to create the scrollView).

----
I would use a nib anyway - much easier to configure the table based on the the text file than to create everything by hand.

----

I have a situation where I too am building a NSTableView by hand and not in IB.  I'm providing a menu that allows the user to choose which table columns are visible.  This breaks the traditional autosave behavior and requires me to code up the information for each field anyway, so it's not a great leap from there to just making it in code to begin with....  The example below should produce a visible table view...

    

NSWindow *window --- should point to your window.
NSView *superView = [window contentView];
NSTableView *tableView = [[NSTableView alloc] initWithFrame:[superView frame]];
[tableView addColumn:[[NSTableColumn alloc] initWithIdentifier:@"field1"]];
NSScrollView *scrollView = [[NSScrollView alloc] initWithFrame:[superView frame]];
[scrollView setDocumentView:tableView];
[superView addSubview:scrollView];



----

If you want the above table view to be free floating in the window instead of taking up the entire window you need to add the following code to display the border:

    

[scrollView setBorderType:NSBezelBorder];



I have also found that by changing the code from using initWithFrame to for the NSTableView to just init the column resizing works properly, not sure why.

