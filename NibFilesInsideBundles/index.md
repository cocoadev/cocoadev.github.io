---
layout: page
title: NibFilesInsideBundles
---

(Based on a mailing list post by Scott Herz.)

Usually the windows you display in your application will come from a NibFile inside your application's main bundle. But this may not always be the case; sometimes it might be better to load a window from some other bundle than your own.

Loading a NibFile from a bundle is a little more difficult than from your 
application code. Why? *+[NSBundle loadNibNamed:] * will only look for the 
NibFile in your main bundle (your app). So, you'll need to use the correct 
bundle to find and load your NibFile. Here's an example:

    
- (void)loadMyNib
{
     //The nib loading code expects parameters in a dictionary. We'll 
make a quick one here.
     //Note: there are easier ways of making a dictionary. Just make sure 
self is the value for @"NSOwner"
     NSDictionary* table = [NSDictionary 
dictionaryWithObjectsAndKeys:self, @"NSOwner", nil];

     //Get your bundle with [NSBundle bundleForClass:[self class]]
     //Pass in the name of your bundle (remember, don't use an extension!)
     //Pass in our table
     //Get your zone (not that we pay much attention to them these days) 
with [self zone]
     [[NSBundle bundleForClass:[self class]] loadNibFile:@"myNib" 
externalNameTable:table withZone:[self zone]];
}

