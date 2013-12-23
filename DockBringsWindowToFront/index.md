---
layout: page
title: DockBringsWindowToFront
---

How do I use a click on the dock icon to open a closed window and bring it to the front?

Thanks.

----


    

-(void)awakeFromNib {
    id center=[NSNotificationCenter defaultCenter];
    [center addObserver:self
                selector:@selector(applicationWillBecomeActive:)
                name:NSApplicationWillBecomeActiveNotification
                object:nil];
}

-(void)dealloc {
    id center=[NSNotificationCenter defaultCenter];
    [center removeObserver:self];
    [super dealloc];
}
-(void)applicationWillBecomeActive:(id)note {
    NSLog(@"applicationWillBecomeActive");
    [window makeKeyAndOrderFront:self];
}



--zootbobbalu

----

Brought back from the dead to answer a new question. That function - (void)applicationWillBecomeActive:(id)note works fine in this case or as the delegate for bringing an app to the front when another app is selected and doing whatever you need to do. 

What I want to know is how to bring the window to the front when the user simply closes the main window and clicks the dock icon without changing to another application. Is there any method to handle that?

-- MatPeterson

----

Try this in your applications delegate:

    
- (BOOL)applicationShouldHandleReopen:(NSApplication *)sender hasVisibleWindows:(BOOL)flag
{
    if (![aWindow isVisible])
        [aWindow makeKeyAndOrderFront:nil];
    return NO;
}


Works great for me

-- KentSutherland

