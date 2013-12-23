---
layout: page
title: UsingFilesStoredInAppDirectory
---

I'm writing a small program that depends on a few (pre-packaged) data files being present for it to work properly.  To that end, I've added them to my Xcode project and now they show up in General/MyApp.app/Contents.  How do I access them? Do I use General/NSApplication to find out the location of my program, then append Contents/General/MyDataFileName to the end of it? Or is there some better way I should be doing this?

----

You should place data files (except for Info.plist, General/PkgInfo and other administrative thingies) in the Resources/ folder of the bundle, by adding them to the Copy Resources phase of your Xcode project, then use General/NSBundle's pathForResource:ofType: method(s) to retrieve their path when needed.

Example, given the following folder structure:
    
 My.app/
  > Contents/
   > > General/MacOS/
    > > > My
   > > Resources/
    > > > General/YourDataFile.datatype
 ...


You can access General/YourDataFile.data's path by doing the following:
    
 - (void) myMethod {
    General/NSString* pathToDataFile = General/[[NSBundle mainBundle] pathForResource:@"General/YourDataFile" ofType:@"datatype"];
    // work with the file...
}


 - General/EmanueleVulcano aka l0ne
