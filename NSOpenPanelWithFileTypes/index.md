---
layout: page
title: NSOpenPanelWithFileTypes
---

We've probably all seen the regular NSOpenPanel sample code from AppleComputer - it goes like this:

    
- (void)openDoc:(id)sender
{
    int result;
    NSArray *fileTypes = [NSArray arrayWithObject:@"td"];
    NSOpenPanel *oPanel = [NSOpenPanel openPanel];

    [oPanel setAllowsMultipleSelection:YES];
    result = [oPanel runModalForDirectory:NSHomeDirectory() 
file:nil types:fileTypes];
    if (result == NSOKButton) {
        NSArray *filesToOpen = [oPanel filenames];
        int i, count = [filesToOpen count];
        for (i=0; i<count; i++) {
            NSString *aFile = [filesToOpen objectAtIndex:i];
            id currentDoc = [[ToDoDoc alloc] initWithFile:aFile];
        }
    }
}


This works only with filename extensions, however. Sometimes it might be desirable to display files that have a certain FileType instead of a filename extension.


To do that, simply change the line 

    
NSArray *fileTypes = [NSArray arrayWithObject:@"td"];


in the above example to something like this:

    
NSArray* fileTypes = [NSArray 
arrayWithObjects:NSFileTypeForHFSTypeCode('TEXT'), nil];


(Based on a a mailing list post by Brant Vasilieff.)

**Question:** Does anyone know if the nil object in the last code line is really necessary?
----

*Answer:* yes. 

You bet your ASCII. Read up on 'arrayWithObjects' in NSArray. This is a varargs style list of inputs:

    
+ (id)arrayWithObjects:(id)firstObj, ...;


The C spec basically requires variable-length parameter lists to declare their parameters explicitly (as in printf) or for lists to be null terminated. (Hmmm ... it's like the difference between pascal strings and c strings.) If you skip the nil your app will die a horrible death as it chews through your program's address space looking for a nil word.

If you really hate nils just change 'arrayWithObjects' to 'arrayWithObject'. arrayWithObject takes only one parameter. But 'arrayWithObjects' is easier to extend when you copy/paste it into your next project ... 

-- MikeTrent

