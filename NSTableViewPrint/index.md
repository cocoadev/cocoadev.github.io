---
layout: page
title: NSTableViewPrint
---

I'm working on an document based applicaton that requires the tableView to be printed. I can get the contents of the tableVew to print just fine but not the colums headers. I would like the column headers to print at the top of every page if thats even possible. The code I used is:
     

-(void)printShowingPrintPanel:(BOOL)flag
{
	NSPrintInfo *printInfo = [self printInfo];
	NSPrintOperation *printOp;
	
	[printInfo setTopMargin:36.0];
	[printInfo setLeftMargin:36.0];
	[printInfo setRightMargin:36.0];
	[printInfo setBottomMargin:36.0];
	[printInfo setHorizontallyCentered:NO];
	[printInfo setVerticallyCentered:NO];
	[printInfo setOrientation:NSLandscapeOrientation];
		
	printOp = [NSPrintOperation printOperationWithView:tableViewPrint printInfo:printInfo];
	[printOp runOperation];

}


This code sets the page up properly except for the scale. I still have to use the page setup window to set that. Any help would be appreciated - Thanks!

