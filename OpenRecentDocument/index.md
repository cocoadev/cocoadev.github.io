---
layout: page
title: OpenRecentDocument
---

Hi,

I want to trap (NSDocumentController) _openRecentDocument in my document based application.

I known that this function is private but can I do that ?

-- Jean-Michel Marino

----

1) Creating a subclass of NSDocumentController like this :

    

#import "AMDocumentController.h"
#import "Controller.h"

@implementation AMDocumentController

-(IBAction) _openRecentDocument:(id)sender
{
	Controller *ctrl = (Controller *)[NSApp delegate];
	
	if([ctrl respondsToSelector:@selector(setFlagOpenRecentFile:)]){
		[ctrl setFlagOpenRecentFile:YES];
	}

	// ---- don't forget...
	[super _openRecentDocument:sender];
}

@end



2) add member m_flagOpenRecentFile and function setFlagOpenRecentFile into your controller class.

3) instantiate this new class into main nib.

-- Jean-Michel Marino

