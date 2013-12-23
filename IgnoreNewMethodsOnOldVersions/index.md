---
layout: page
title: IgnoreNewMethodsOnOldVersions
---

In my application I'm using:
    
- (NSArray *)toolbarSelectableItemIdentifiers:(NSToolbar *)toolbar
{
     return [NSArray arrayWithObjects:@"General",@"Misc",@"Updates",@"Registration",NSToolbarFlexibleSpaceItemIdentifier,nil];
}


Which puts the nice little boxes around selected toolbar items (like in Panther prefs), when you do this:
    
[toolbar setSelectedItemIdentifier:@"General"];


The problem is, while I can do this to avoid running that on 10.2:
    
if ([toolbar respondsToSelector:@selector(setSelectedItemIdentifier:)]) {
	[toolbar setSelectedItemIdentifier:@"General"];
}


I don't know how to avoid 10.2 seeing the method itself. Because of this, this class doesn't work in Jaguar. Is there a way to do something like:
    
if ([toolbar respondsToSelector:@selector(setSelectedItemIdentifier:)]) {
     - (NSArray *)toolbarSelectableItemIdentifiers:(NSToolbar *)toolbar
     {
          return [NSArray arrayWithObjects:@"General",@"Misc",@"Updates",@"Registration",NSToolbarFlexibleSpaceItemIdentifier,nil];
     }
}


In the class? Obviously that won't work, but something like it?

Help!

Thanks,
GarrettMurray

----

I might just be being dense, but how does this method's existence keep it from running in Jaguar?  I'd think that the     -toolbarSelectableItemIdentifiers: delegate method would just never get called in Jaguar.  -- Bo

----

Actually, you're quite right Bo, and it was me who was being dense. I just responds-checked any sets and it's running perfectly. Sorry about that. --GarrettMurray

