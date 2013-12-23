---
layout: page
title: HowToOpenASheet
---

To open a sheet you need to have two things, the window the sheet will be attached to and the window that's the sheet's contents.

To open the sheet:
    
[NSApp beginSheet:aSheet
         modalForWindow:aWindow
         modalDelegate:aWindow
         didEndSelector:nil
         contextInfo:nil];


Where aSheet is the sheet you want to display and aWindow is the window it will be attached to.

To close the sheet:
    
[NSApp endSheet:aSheet];
[aSheet orderOut:nil];


Where aSheet is the sheet.

The usage of sheets is described in Mac OS X Human Interface Guidelines.

-- MatPeterson

