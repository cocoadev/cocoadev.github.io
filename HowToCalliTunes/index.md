---
layout: page
title: HowToCalliTunes
---

I am writing an application that needs to call other applications. What I am trying to do as an experiment to start with is call iTunes and get it to do something. What is the best way of going about this? Is there some way of adding applescript to my code to achieve this?

----

You can use either AppleScript or AppleEvents. AppleScript is the easiest way to go in 10.2, but not 10.1. You can do something like

    
 NSDictionary		*errorDict;
 NSAppleEventDescriptor 	*returnDescriptor;
 NSAppleScript 		*scriptObject = NSAppleScript alloc] initWithSource:
                                          @"tell application \"itunes\" \n playpause \n end tell"];
 
 NSLog(@"executing script");   returnDescriptor=[scriptObject executeAndReturnError: &errorDict];
 [scriptObject release];
 
 if ([returnDescriptor descriptorType]) {
   //	The execution succeeded
   NSLog(@"script executed sucessfully.");
   if (kAENullEvent!=[returnDescriptor descriptorType]) {
     //	The script returned an AppleScript result
     if (cAEList==[returnDescriptor descriptorType]) {
       //	Result is a list of other descriptors
       ...
       do something iterative with the list
 		...
         } else {
           ...
           co-erce the result to the appropriate [[ObjC type
           ...
         }
   } else {
     NSLog(@"AppleScript has no result.");
   }
 } else {
   NSLog(@"Script execution has gone a bit PeteTong: %@", [errorDict objectForKey: @"NSAppleScriptErrorMessage"]);
 }


