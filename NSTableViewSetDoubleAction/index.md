---
layout: page
title: NSTableViewSetDoubleAction
---

Hopefully, I can get some help.  Basically, I'm having some trouble getting an action to trigger from the setDoubleAction.  I made it Not-Editable, for the table view.  Isn't that all I have to do?  I have the datasource and delegate set.  So... basically, I've looked at examples and seen the following inside of awakeFromNib.  This is from memory, so I may be a bit off.  I've followed the syntax directly though.

[myTable setDoubleAction:@interface:play] or something like that.  I don't think my problem is the syntax, but I'd really like it if someone can explain this to me.  I can never get it to work, and I'm a complete novice... I don't want to give up on cocoa, but it seems to get more complicated the longer I spend playing around with it.  Thanks a lot.  You guys are my last resorts.

-Gumpan

@interface is what you use to declare new classes. What you want is something like [myTable setDoubleAction:@selector(play:)] -- RobRix

----

you didn't set the target class. For the double click to work,  you need both (setTarget and setDoubleAction:) . put into your awakeFromNib:

[tableViewName setTarget:self];

I assume that the class self contains play:

Ted

----
I'd just like to mention here (it seemed the most appropriate place) that this behavior has changed in Leopard.  Now, the doubleAction get's fired for editable cells too.  To get back the old behavoir, do something like:
    
- (IBAction)doDoubleClick:(id)sender {
	if([sender clickedRow] == -1){
              //your action for clicks on the column headers and empty space
	}
	else {
		[myTable editColumn:[sender clickedColumn] row:[sender clickedRow] withEvent:nil select:YES];
	}
}

.wch

