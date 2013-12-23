---
layout: page
title: TableColumnHeaderRightClick
---

Describe General/TableColumnHeaderRightClick here.

im trying to get a contextual menu to pop-up when a column header is right clicked to add and remove columns ala itunes. i've already added view options where you can add and remove columns via checkboxes (like itunes) but i also want to have right click work in the column header and can't figure it out for the life of me. the tables themselves have contextual menus so i already have that taken care of (thanks to alot of the information on cocoadev :) ) any help would be super appreciated.

----

An General/NSTableHeaderView is simply a subclass of General/NSView.  Subclass it and add your own methods for right clicking such as popping up a contextual menu (lots of information on this site, especially useful may be the General/RightClickSelectInTableView page).  Then use General/NSTableView's setHeaderView: method to add it.

----

This can be implemented this by doing the following:

The window controller has the following in the @interface of the .h-file:

    
General/IBOutlet General/NSTableView *theTable;
General/IBOutlet General/NSMenu *columnsMenu;
General/IBOutlet General/NSTableColumn *tcColumnOne;
General/IBOutlet General/NSTableColumn *tcColumnTwo;
General/IBOutlet General/NSTableColumn *tcColumnThree;


In the corresponding .nib-file, an General/NSMenu needs to be created with one menu item for each column in the table. These should have their tag set to 0, 1, 2 etc.

The variable columnsMenu should be connected to the created General/NSMenu, in the controller. Each of the above General/IBOutlets should be connected to their corresponding General/NSTableColumns. theTable should be connected to the table containing the columns.

Then create a method in the controller:

    
- (General/IBAction)toggleColumn:(id)sender
{
    General/NSTableColumn *tc = nil;

    switch([sender tag])
    {
        case 0: tc = tcColumnOne; break;
        case 1: tc = tcColumnTwo; break;
        case 2: tc = tcColumnThree; break;
    }

    if(tc == nil)
        return;
    
    if([sender state] == General/NSOffState)
    {
        [sender setState:General/NSOnState];
        [theTable addTableColumn:tc];
    }
    else
    {
        [sender setState:General/NSOffState];
        [theTable removeTableColumn:tc];
    }
}


"theTable" should be the variable name of your table. Then connect each menu item in the created General/NSMenu to the toggleColumn method, as action.

And to complete this, add the following code to the awakeFromNib method in your controller:

    
- (void)awakeFromNib
{
    [tcColumnOne retain];
    [tcColumnTwo retain];
    [tcColumnThree retain];
    
    General/NSArray *tcs = [theTable tableColumns];
    General/NSEnumerator *e = [tcs objectEnumerator];
    General/NSTableColumn *tc;
    while((tc = [e nextObject]) != nil)
    {
        if(tc == tcColumnOne)
            General/columnsMenu itemWithTag:0] setState:[[NSOnState];
        else if(tc == tcColumnTwo)
            General/columnsMenu itemWithTag:1] setState:[[NSOnState];
        else if(tc == tcColumnThree)
            General/columnsMenu itemWithTag:2] setState:[[NSOnState];
    }
    
    [[theTable headerView] setMenu:columnsMenu];
}


And don't forget to release the columns in the dealloc method:

    
- (void)dealloc
{
    [tcColumnOne release];
    [tcColumnTwo release];
    [tcColumnThree release];
    
    [super dealloc];
}


I may have missed something, but try that out.
