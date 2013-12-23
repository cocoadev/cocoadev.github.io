---
layout: page
title: OpenSheet
---

I tried to open a new sheet with:

[NSApp beginSheet:loeschenSheet modalForWindow:mainWindow modalDelegate:self didEndSelector:NULL contextInfo:nil];

It worked perfectly.


Then I tried to send an Action from another class to my sheetController, which should then open the sheet, but this is what I got:

2002-06-29 21:11:24.287 MyApp[6870] *** Assertion failure in -[NSApplication _commonBeginModalSessionForWindow:relativeToWindow:modalDelegate:didEndSelector:contextInfo:], NSApplication.m:2306
2002-06-29 21:11:24.299 MyApp[6870] Modal session requires modal window

Tried it with another action, and it worked, just not with this.


Hope this makes sense. *g*

-- MartinWeil

Hmmm can you be a little more detailed? what action did, what action didnt?

----

I am having the same issue, calling a sheet from a different class causes that. This is what I got:

DXTableViewController.m
    
#import "DXTableViewController.h"
#import "DXApplicationController.h"

DXApplicationController* dxAppControl;

@implementation DXTableViewController

- (IBAction)addNewTask:(id)sender {
dxAppControl = [[DXApplicationController alloc] init];
[dxAppControl beginSheetForTaksAddition];
}


DXApplicationController.m
    
#import "DXApplicationController.h"
#import "DXTableViewController.h"

- (void)beginSheetForTaskAddition {
[NSApp beginSheet:addSheet modalForWindow:[NSApp keyWindow] modalDelegate:NULL didEndSelector:NULL contextInfo:NULL];
}


I get the same error as our friend above:
    
2005-07-05 21:47:41.553 QuickTask[416] *** Assertion failure in -[NSApplication _commonBeginModalSessionForWindow:relativeToWindow:modalDelegate:didEndSelector:contextInfo:], AppKit.subproj/NSApplication.m:2763
2005-07-05 21:47:41.554 QuickTask[416] Modal session requires modal window


Any ideas? - FernandoLucasSantos

----

Normally this error occurs because your sheet is nil. Check in the debugger to make sure that     addSheet is actually a valid NSWindow.

