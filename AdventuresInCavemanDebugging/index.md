---
layout: page
title: AdventuresInCavemanDebugging
---

I had problems with my application having a delayed crash. By delayed crash I mean I perform a few actions and the next time I try to perform a KeyEquivalent (could be command H, command Q etc) it crashes. These same methods when initiated by selecting the menu item do not result in a crash (when called again directly afterwards with a key equivalent it causes a crash). 

I have tried setting the environment variable NSZombieEnabled with no luck so I do not believe it is a deallocated pointer being accessed. Actually I have little idea what it could be as I haven't had any luck in discovering the bug.

However I have pin pointed the location in code where it sets off the chain of events (using caveman debugging). Firstly however I will post crashed thread from the crash log:

    
 Exception:  EXC_BREAKPOINT (0x0006)
 Code[0]:    0x00000001
 Code[1]:    0x98f38e80
 
 
 Thread 0 Crashed:
 0   libobjc.A.dylib      	0x98f38e80 _objc_trap + 0
 1   libobjc.A.dylib      	0x98f38db8 _objc_error + 0x50
 2   libobjc.A.dylib      	0x98f38d44 __objc_error + 0x40
 3   com.apple.AppKit     	0x92f87160 -[NSApplication _handleKeyEquivalent:] + 0xa8
 4   com.apple.AppKit     	0x92ea4840 -[NSApplication sendEvent:] + 0xa5c
 5   com.apple.AppKit     	0x92ead0d0 -[NSApplication run] + 0x240
 6   com.apple.AppKit     	0x92f697bc NSApplicationMain + 0x1d0
 7   main.ob              	0x004cae78 main + 0x38 (main.m:30)
 8   com.triage.cuisine2  	0x00001c04 start + 0x1c8
 9   dyld                 	0x8fe1a278 _dyld_start + 0x64


Not very helpful eh? Now what I have found out from using caveman debugging is that it crashes if this line is called:

    
 [workingRules addObject:[TRSmartGroupRule object]];
 [editTable reloadData];


However here is the interesting thing, it is not the actual rule but reloading the table which causes the crash. Because of this I must assume that the object accessing methods are causing the delayed crash right (because simply adding the object to the array does not cause the crash)?

So I go ahead and comment out all the table data source methods and other then it whining in the log of an improper data source everything is fine. Of course this seems promising so I decide to implement a basic delegate while leaving the original one commented:

    
 - (int)numberOfRowsInTableView:(NSTableView *)table
 {
 	return 5;
 }
 
 - (id)tableView:(NSTableView *)table objectValueForTableColumn:(NSTableColumn *)column row:(int)row
 {
 	return @"pie";
 }


Omg it crashes again! So here we can conclude it isn't the actual original data source methods that are causing the crashes. Hrmph. Well what other elements are in the table? There are the cells, the headers... that is all I actually configure, now since I had to create this table entirely in code let me head up to where I created them. There we are, lets work with the popupbutton cells first since they have menus and menuitems can have key equivalents (hell its the logical step of action...):

    
 NSPopUpButtonCell * applyDataCell = [NSPopUpButtonCell alloc] initTextCell:@""
 	 pullsDown:NO] autorelease];
 [applyDataCell setControlSize:NSMiniControlSize];
 [applyDataCell setMenu:[self fieldsMenu;
 
 applyColumn = NSTableColumn alloc] initWithIdentifier:@"Apply"];
 [applyColumn setMaxWidth:100];
 [applyColumn setDataCell:applyDataCell];
 [editTable addTableColumn:applyColumn];


The thing is i'm already wondering how this could cause a crash, its called when creating the window and that is done regardless of if we have items in the table view.

So lets comment out that cell (and another virtually identical cell below). *Launches program* Omg it works, no more crashing on key equivalents. Woo. Lets test that again just to make sure. *relaunches program, creates new smart group,  saves, right clicks on newly created smart group, goes to remove, selects remove, yes I want to remove this, command-h* woo no crashes! *uncomments old data source code and runs* Woo no crash.

So now that we have pretty much pinned down where the crash so half hazardly originates all we can do now is attempt to fix it. *uncomments all the applyDataCell code except     -[NSPopUpButtonCell setMenu:]* *tests* Woos no crash. *uncomments other cell code except for     -[NSPopUpButtonCell setMenu:]* ARG crash. Or should it be yay crash since we basically narrowed it down to the following code?:

    
 NSPopUpButtonCell * operatorDataCell = [[[NSPopUpButtonCell alloc] initTextCell:@""
 	pullsDown:NO] autorelease];
 [operatorDataCell setControlSize:NSMiniControlSize];
 //[operatorDataCell setMenu:[self operatorMenu;
 [operatorColumn setDataCell:operatorDataCell];


I think for good measure we should comment that up again and test it just to make sure the first cell doesn't crash as well. *test* No crash. Lets see if adding a menu to the first cell causes a crash. *uncomments     [applyDataCell setMenu:[self fieldsMenu]];* No crash!

So now we have the first column working perfectly.

I copy and paste the code for the second column, it is identical except I don't set a menu. Crash. Wtf! Ok, so we have a crash if we have an NSPopUpButtonCell in a table column, the code is identical to the above column which doesn't crash....

Grr! Okay what else can I check, the instance variable is named operatorColumn, we can always check that, but its a long shot, lets check if it is released properly. Oooh it *isn't* released in dealloc!!! Maybe this isn't it *adds     TRRelease(operatorColumn);* *runs* Omg omg omg it doesn't crash!!! Yay!!!

Now I can see why it was crashing and all, hell NSTableColumn has a     -setTableView: so if it tried to access that later when the key equivalent was called it would SIG and crash, however it was DAMN hard to find and the crash log wasn't helping *at all*. This is a perfect example however why you should check that all instance variables are properly deallocated if you are receiving strange crashes.

And so concludes my adventures in caveman debugging.


----
The problem is that you inadvertently leaked the NSPopUpButtonCell and NSApplication keeps track of every pop up cell to look for key equivalents. As it looks for the key equivalent, it asks the cell for it's -controlView which has been properly freed so then when it tries to message the control view it crashes.

In my project, in my application I added a category to NSPopUpButtonCell to prevent such crashes:
    
 @implementation NSPopUpButtonCell (AppKitBugFixes)
 - controlView;
 {
     id controlView;
 
     controlView = [super controlView];
     if (controlView != nil && !NSZoneFromPointer(controlView)) {
         NSLog(@"invalid control view %p detected for popup: %@ title:%@ items: %@", 
                 controlView, self, [self title], [[self itemArray] description]);
         /* the only time I've seen this is if we got accidentally leaked 
            and if the condition persists, the application will crash */
 #warning autoreleasing popups with bad controlViews
         NSLog(@"autoreleasing affected popup");
         [self autorelease];
         return nil;
     }
 
     return controlView;
 }
 
 @end

----

