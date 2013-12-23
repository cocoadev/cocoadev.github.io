---
layout: page
title: ShowAlertAndWriteToLog
---

This code will show an alert panel, and if the user clicks the OK button, write to the log file.

    
if (NSRunAlertPanel(@"Warning", @"Do you really want to blah blah blah?", @"OK", @"Cancel", NULL) == NSOKButton) 
{
    NSLog(@"The OK button was pressed.");
}

