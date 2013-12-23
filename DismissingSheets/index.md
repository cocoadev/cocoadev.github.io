---
layout: page
title: DismissingSheets
---

When I saw the following     [NSApp beginSheet:mySheet modalForWindow:mainWindow modalDelegate:nil didEndSelector:nil contextInfo:nil]; My sheet pops right up.  But when I click my button on the sheet, which is wired up to a dimiss method, nothing I seem to do makes the sheet go away.  Does anyone know how to dismiss sheets? (I tried, [NSApp endSheet:mySheet] but that doesn't seem to help)

----

Did you try 
    
[NSApp endSheet:mySheet];
[mySheet orderOut:nil]

