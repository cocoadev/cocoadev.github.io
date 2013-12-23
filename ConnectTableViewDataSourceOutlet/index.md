---
layout: page
title: ConnectTableViewDataSourceOutlet
---

I have a table view in my project that I wired up with interface builder ('IBOutlet NSTableView *citySceneTable;' in interface) and I have two of the protocol methods set to return appropriate values (numberOfRowsInTableView and 'tableView: objectValueForTableColumn...'), both set to return appropriate results. The methods are never ever called though, even when I explicitly instruct them to be [citySceneTable reloadData]! I'm sure everything is set up right, because I closely mirrored a tutorial, but still, it just never works.

And the question: am I forgetting something? Here's everything I've done: 1. set up the view in IB, and wired it up as an outlet from my instantiated main class 2. declared the above two mentioned methods in the interface 3. implemented them. That's the only thing I've done with the citySceneTable. I set up to test it, and even threw in a button to call the [citySceneTable reloadData] method. But nothing happens; not even the numberOfRowsInTableView method is called. Am I supposed to init it somehow? or am I supposed to explicitly state it's data source? Any idea at all?

(By the way, the data source is a NSMutableArray declared in the header. In testing, I've initted it with one object, so the numberOfRowsInTableView initially returns 1 ([myArray count]). The objectValueForTableColumn implementation is 'return myArray objectAtIndex:rowIndex] valueForKey: [aTableColumn identifier;', and the column identifiers are correctly set up.)

----

It sounds like perhaps you missed one important thing: making your data source object the table's data source by wiring the table view up with the data object. In other words, you have to control-drag from your table view (make sure you double-click on the table view so it isn't the scroll view that's selected) to your data source object, and double-click on the dataSource item in the Outlets list in the Inspector. Make sense? I hope that does the trick for you. Good luck!

-- JaredWhite

Or set the data source programmatically via 

    [citySceneTable setDataSource:self];

Where self is the object that actually implements the table data source protocol.

-- MikeTrent

see also NSTableViewTutorial and NSTableViewDataSource

