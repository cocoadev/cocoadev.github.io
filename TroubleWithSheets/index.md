---
layout: page
title: TroubleWithSheets
---

In the console when I try to invoke a sheet I get this warning:

2004-09-21 19:57:19.285 General/HarborMaster[26862] *** -General/[NSView _setSheet:]: selector not recognized
2004-09-21 19:57:19.286 General/HarborMaster[26862] *** -General/[NSView _setSheet:]: selector not recognized


What's wrong? The code I used:

    - (General/IBAction)addItem:(id)sender
{
        General/[NSApp beginSheet: addSheet
                        modalForWindow: mainWindow
					
                        modalDelegate: self
                        didEndSelector: @selector(sheetDidEnd: returnCode: contextInfo:)
                        contextInfo: nil];}


----

Haha, nevermind - turns out I had addSheet connected to an General/NSSearchField instead of my General/NSPanel.
