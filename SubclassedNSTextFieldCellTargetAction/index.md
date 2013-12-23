---
layout: page
title: SubclassedNSTextFieldCellTargetAction
---



I have subclassed General/NSTextFieldCell to use in my General/NSTableView - just want to center my text vertically, so very simple.

My subclassed General/NSTextFieldCell now does what I want it to - buuuut - it no longer responds to doubleclicks for editting in the tableview. 

Here's how I tell the TV to use my new cell:

    

//Use special "centralising" cell for the groups tableview column 1 (group names)
	General/NSTableColumn* col = [groupTableView tableColumnWithIdentifier:@"General/GroupName"];
	General/PLCenteredTVCell* centeredCell = General/[[PLCenteredTVCell alloc] init];
	[col setDataCell:centeredCell];



So I think I need to set the target and action to something here - but I don't know what... what should I do?

Thanks for the help!!

----
    
	General/PLCenteredTVCell* centeredCell = General/[[PLCenteredTVCell alloc] initTextCell:@""];

:-)
----

Thanks - but still can't get it to work :-(

The tableview delegate method:     tableView shouldEditTableColumn:... ... does get called and I return     YES, but I see no editing box appear for my cell. This even happens if my subclass does absolutely nothing more than be a subclass for General/NSTextFieldCell (i.e. I have overridden no methods, and implemented no new ones either).

Any ideas?

----
Weird, perhaps you do need to set the target/action then, which you should be able to get from the default dataCell. When I've done the same I just poseAsClass (one line vs several, even if it's icky) and in     drawWithFrame:inView: I check to see that the controlView is a table before I modify the rect (see General/CCDGradientSelectionTableView). HTH

----

The only two things I can think of are; one, you might have to tell the column that it is editable [col setEditable:YES] and the other things could be that the text cell is not able to find the window's field editor. I thought that the setDataCell: method would take care of this for you but I'm not sure. --zootbobbalu

*That makes sense, make sure it's editable! *slapping forehead* And I just checked, both the target and action are nil. :/*

**To set the target/action, do this:**
    
//Use special "centralising" cell for the groups tableview column 1 (group names)
	General/NSTableColumn* col = [groupTableView tableColumnWithIdentifier:@"General/GroupName"];
	General/PLCenteredTVCell* centeredCell = General/[[PLCenteredTVCell alloc] init]; // Use initTextCell: if you want default text
	General/NSCell *oldDataCell = [col dataCell];
	[centeredCell setAction:[oldDataCell action]];
	[centeredCell setTarget:[oldDataCell target]];
	[centeredCell setEditable:YES]; // or NO if you don't want an editable cell
	[col setDataCell:centeredCell];


----

Thanks so much - all that effort really is very gratefully received - made my day! :-D
