---
layout: page
title: AnotherNSTableViewTutorial
---




I thought I might as well give back to the community by creating a tutorial on how to use  NSTableViews a different way, and, in my opinion a better way then most of the ones I have read. This will be a step-by-step guide.

----

** 1. Setting up your Nib **


* Open the nib which you wish to add a table to.
* Drag a new NSTableView to the view you wish it to display in.
* Select the "classes" tab in the nib window.
* Double-click the NSTableView to select the actual NSTableView and not the NSScrollView.
* Go back to the nib window, NSTableView should be selected in the browser, press enter to subclass it, use a name such as TRTableView -- something to describe the object.
* In the 'Classes' menu create the files for the classes.
* Select the TableView then open the info window if it is not open, choose "Custom Class" from the popup menu and select the name of your subclass (eg. TRBuddyList).
* Return to XCode, open your newly created class and change your header to include the following:

    
{
	NSMutableArray * items;
}

/* Accessor */
// Returns a mutable array with dictionaries of all the items
- (NSMutableArray *)items;
- (id)selectedRowItemforColumnIdentifier:(NSString *)anIdentifier;

/* Mutator */
- (void)setItems:(NSMutableArray *)anArray;
- (void)addRow:(NSDictionary *)item;
- (void)removeRow:(unsigned)row;

// IBActions


* Then add this to the implementation file:

    
- (void)awakeFromNib {
	items = [NSMutableArray new];
	[self setDelegate:self];
	[self setDataSource:self];
	[self reloadData];
}

- (void)dealloc {
	[items release];
	[super dealloc];
}


** 2. Next we need to add the accessor methods **
    
- (NSMutableArray *)items {
	return items;
}

- (id)selectedRowItemforColumnIdentifier:(NSString *)anIdentifier {
	if ([self selectedRow] != -1)
		return items objectAtIndex:[self selectedRow objectForKey:anIdentifier];

	return nil;
}

** 3. Next we need to add the mutator methods **
    
- (void)setItems:(NSMutableArray *)anArray {

	if (items == anArray)
                return;
        [items release];
	items = anArray;
        [items retain];
	[self reloadData];
}

- (void)addRow:(NSDictionary *)item {
	[items addObject:item];
	[self reloadData];
}

- (void)removeRow:(unsigned)row {
	[items removeObjectAtIndex:row];
	[self reloadData];
}

** 4. And finally add the data source objects: **
    
- (int)numberOfRowsInTableView:(NSTableView *)tableView {
	return [items count];
}
- (id)tableView:(NSTableView *)tableView objectValueForTableColumn:(NSTableColumn *)tableColumn row:(int)row {
	if (row != -1)
		return items objectAtIndex:row] objectForKey:[tableColumn identifier;

	return nil;
}


So if you want to add items to this array, just set an NSMutableArray populated with NSDictionaries whose keys match the column identifiers! Very easy, and very reusable, very nice for showing lots of info with little code, I use this technique all the time and load contents from plist files, which makes it even easier to populate your tableview with the least amount of code as possible!

One thing to note however is the method addRow: if you are adding more than one or two things, you may encounter problems so it is best to use setItems:.

I typed this up in class so once I get home I will verify the code and post a project for download for anyone that is new to this as this was a pain to figure out when I was new to the whole developing scene.

Later I will add to this how to make a custom cell close to what people can see in iChat.

** Comments: **
----

Hello, this is Uwe,
I felt lucky to find such a simple sample about NSTableView -but-
it didn't. I get this:
Illegal NSTableView data source (()).  Must implement numberOfRowsInTableView: and tableView:objectValueForTableColumn:row:
Changing a line in awakeFRomNib :  [self setDataSource:[self items]]; and added this line to setItems I got:

    
Illegal NSTableView data source ((
    {
        ID = 0; 
        frequence = 1; 
        phaseX = "0.000000"; 
        phaseY = "0.000000"; 
        phaseZ = "0.000000"; 
    }, 
    {
        ID = 1; 
        frequence = 1; 
        phaseX = "0.000000"; 
        phaseY = "1.570796"; 
        phaseZ = "0.000000"; 
    }
)). 

 Must implement numberOfRowsInTableView: and tableView:objectValueForTableColumn:row:

Can you see what's wrong ?
Uwe
----
You don't set the data source to your      , you set it to your *controller*, and it then accesses the       to provide the responses.
----
I remember, that was my first approach.From       i sent source to tableView, I got no error, but the view is empty.
Uwe
----
I don't know how many people check this post but can anyone give me instructions on how to load a file called: Bookmarks.plist that is packaged in the application bundle into the array and then into the table view. It is an Apple formatted plist file and I have implemented the NSTableView as per the insturctions above, the application compiles and runs without errors.
----
You can certainly read any plist file, but you will have to be careful to understand the format. Few plists are just flat arrays. They often contain specific types of values. Remember a plist is xml. It is a node structure, and each node can be a different data type that must be handled properly. You will need to put lots of checks in place to make sure the plist you are handling works the way you think it does. If if it has a standard, documented format, you should be ok if it is versioned. Otherwise, you may not have a complete understanding of the data types that might be possible in the plist.
----
Cleaned up the setItems method. (24 Apr 06) -- JoshuaPenn
----
How do you access this table from a different class? Do you need to [[[TRTableView alloc] init] selectedRow] or create your own "accessor" type methods? I have one class that controls a nib with tabs (each tab having its own table) and I'd like to have a class for each table to separate out all the code. How can the class that controls the nib access the objects that are in the tables?

Thanks
-Danprime
----
Removed warnings in - (id)selectedRowItemforColumnIdentifier:(NSString *)anIdentifier and - (id)tableView:(NSTableView *)tableView objectValueForTableColumn:(NSTableColumn *)tableColumn row:(int)row . -- SamSoffes

