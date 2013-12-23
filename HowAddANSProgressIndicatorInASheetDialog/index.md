---
layout: page
title: HowAddANSProgressIndicatorInASheetDialog
---

I make a custom sheet dialog with a NSProgressIndicator to wait until my App is connected to a MySQL server. But because the sheet is a modal window, I can't do jobs for testing the connexion while the sheet runs. 
Can sombebody give me a starting point to implement this? 
Have I to use NSThread to accomplish this ?
----
Solved : it was simple. Just add      [progBarre setUsesThreadedAnimation:YES];  (where progBarre is the NSProgressIndicator animated progression bar)
    
/* progWindow is a NSWindow that contains the NSProgressIndicator, created in IB */
[NSApp beginSheet: progWindow
       modalForWindow: mainWindow
        modalDelegate: self
       didEndSelector: NULL
          contextInfo: nil];
    [progBarre setUsesThreadedAnimation:YES];
    [progBarre display];
    [progBarre startAnimation: self];
    
    /* MySQL server stuff */
    [serveur initLogin: loginPrefs];
    Err = [serveur erreursIntSQL];
    
    /* close the sheet */
    [progWindow orderOut: self];
    [NSApp endSheet: progWindow];


Now the next step is to create a NSTimer that close the sheet after a time delay if the server do not respond.
    -keefaz

