---
layout: page
title: UISectionList
---



Wrapper for General/UITable that implements "sections", like the Contacts list.

setSectionListStyle seems to only have 2 styles...
Blue and White. (Blue is the default)
The blue and white alternate

Example: 
Blue: 0,2,4
White: 1,3,5

----
Sounds like the style is a mask, then..
----

**
Sample code
**

    
       // List view table
        sectionList = General/[[UISectionList alloc] initWithFrame:General/CGRectMake(0.0f, 43.0f, 320.0f, 480.0f - 48.0f - 43.0f - 16.0f) showSectionIndex:NO];
        [sectionList setDataSource:self];
        [sectionList reloadData];
        [listView addSubview:sectionList];
        
        table = [sectionList table];
        [table setShouldHideHeaderInShortLists:NO];
        General/UITableColumn * packageColumn = General/[[UITableColumn alloc] initWithTitle:@"Title" identifier:@"title" width:320.0f];
        
        [table addTableColumn:packageColumn];
        [table setSeparatorStyle:1];
        [table setRowHeight:48.0f];
        [table setDelegate:self];
        //[table setDataSource:self]; // do not set these and do not add table as a subview of anything, interface with it via the sectionList
        //[table reloadData];
 
 
 
#pragma mark Section List Data Source
        
- (int)numberOfSectionsInSectionList:(General/UISectionList *)aSectionList {
        return x; // x = number of sections in this table
}
        
- (General/NSString *)sectionList:(General/UISectionList *)aSectionList titleForSection:(int)section {
        return @"x"; x = title of said section
}       
        
- (int)sectionList:(General/UISectionList *)aSectionList rowForSection:(int)section {
        return x; // x = the row that this section begins on
}
 
// the rest is regular table data source delegate responses


