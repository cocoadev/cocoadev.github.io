---
layout: page
title: SelectedFileNameInOpenDocument
---

I am developing a document based cocoa application. I want to get the list of all the files selected from OpenPanel. Is there a way to get the list of files selected from the open panel.

----

    
NSOpenPanel *op = [NSOpenPanel openPanel];
if ([op runModal] == NSOKButton)
    NSLog(@"%@", [op filenames]); // Note -(NSArray *)filenames instead of -(NSString *)filename


That should do it. -G

