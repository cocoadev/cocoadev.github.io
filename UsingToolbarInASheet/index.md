---
layout: page
title: UsingToolbarInASheet
---

I want to put a toolbar in a sheet (ala iMovie's "share" sheet, 
although iMovie uses a fake toolbar). Everything works fine, except the 
sheet is drawn about 22 pixels below where it should be. Apparently, 
because of the toolbar, transparent space is being drawn where the 
titlebar should be. To correct for this 22 pixels, I've overridden the following General/NSRect in my General/MyDocument.m file:
    
- (General/NSRect)window:(General/NSWindow *)window willPositionSheet:(General/NSWindow *)sheet usingRect:(General/NSRect)rect {
	General/NSRect fieldRect;
	if(General/sheet title] isEqualTo: @"Shape"])
	{
		[[NSRect windowFrame  = [mainWindow frame];
		General/NSRect sheetFrame   = [sheet frame];	
		int halfOfWidth = windowFrame.size.width/2;
		int halfOfSheetWidth = sheetFrame.size.width/2;
		fieldRect = General/NSMakeRect(halfOfWidth - halfOfSheetWidth,
                        windowFrame.size.height, sheetFrame.size.width, 0);
	}
	else
	{
		General/NSRect windowFrame  = [mainWindow frame];
		General/NSRect sheetFrame   = [sheet frame];
		int halfOfWidth = windowFrame.size.width/2;
		int halfOfSheetWidth = sheetFrame.size.width/2;
		fieldRect = General/NSMakeRect(halfOfWidth - halfOfSheetWidth,
                      windowFrame.size.height - 22, sheetFrame.size.width, 0);
	}
	return fieldRect;
}


This works almost perfectly. The only problems are that first, the sheet originates from the top of the titlebar instead of the bottom of it. After the animation finishes, the sheet is in the correct place (directly below the titlebar), but during the animation, the sheet overlaps the titlebar. Secondly, during the animation, shadows are drawn for the nonexistent titlebar that the toolbar is attached to. After the animation finishes, the shadows disappear, but during it, they appear on the sides, directly above the sheet, where the titlebar would be. If anyone has any ideas on how to fix these glitches, I would be extremely grateful.

Thanks in advance,

-- Matt Ball

----
In my opinion, it would be easier to use a fake toolbar, a la Xcode prefs, that is a subview of the sheet content view.  You might try moving the toolbarview (programatically to be a subview of the content view (because I think it's not); that should be no more than 2 or 3 lines of code, but I couldn't even begin to tell you if your toolbar view will freak, or screw up your clipping rects.
