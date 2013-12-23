---
layout: page
title: NSOutlineViewFileManager
---

Is there any quick and easy to set a NSOutlineView to a certain folder contents?

----

The example project at file:///Developer/Examples/AppKit/OutlineView/ shows how to do this.

----

I (not the original poster), tried this out, but it wouldn't work for anything other than the startup disk. I couldn't get it to work with dmgs, external hard drives's etc...
----
I looked at the example, very nice. But how would I access the file icon and display in the view?
----
I tried this example aswell.  Is there any way to make it work with not just your root drive?  Thanks

*See the comment about making an array of your root items and returning nil on the NSOutlineView page*

**Also note that all mounted disks show up in /Volumes. So a CD named "Photos 2003" would show up as "/Volumes/Photos 2003/"**

The startup volume isn't in /Volumes. **{Yes, it is}** And the name of the mount point in /Volumes isn't necessarily the same as the actual volume name. You shouldn't rely on it.

----

Feeding the example "/Volumes/" does not list all volumes, it just has the text 'Volumes' at the top as a non expandable item. Any ideas as to how to do it?

Works fine like this (to list all volumes) Just change the two D<nowiki/>ataSource methods to match this:
    
- (int)outlineView:(NSOutlineView *)outlineView numberOfChildrenOfItem:(id)item {
    return (item == nil) ? [FileSystemItem numVolumes] : [item numberOfChildren];
}

- (id)outlineView:(NSOutlineView *)outlineView child:(int)index ofItem:(id)item {
    return (item == nil) ? [FileSystemItem volumeAtIndex:index] : [item childAtIndex:index];
}

Then add these two methods to F<nowiki/>ileSystemItem:
    
+ (FileSystemItem *)volumeAtIndex:(int)index {
	if (volumeDir == nil)
		volumeDir = [[FileSystemItem alloc] initWithPath:@"/Volumes" parent:[FileSystemItem rootItem]];
	
	return [volumeDir childAtIndex:(index + 1)]; // +1 because of .DS_STORE. This really shouldn't be hardcoded.
}

+ (int)numVolumes {
	if (volumeDir == nil)
		volumeDir = [[FileSystemItem alloc] initWithPath:@"/Volumes" parent:[FileSystemItem rootItem]];

	NSLog(@"%i", [volumeDir numberOfChildren]);
	return [volumeDir numberOfChildren] - 1; // -1 because of .DS_STORE. Ditto
}

Probably your problem was that you didn't set the parent to a real F<nowiki/>ileSystemItem. The code for F<nowiki/>ileSystemItem requires that you have all parent objects set (except for /) in order to generate a valid path. If you wanted to make an arbitrary root, do it like this:
    
+ (FileSystemItem *)rootItemWithPath:(NSString *)path {
	if ([path isEqualToString:@"/"]) return [FileSystemItem rootItem];
	else {
		NSString *parentPath = [path stringByDeletingLastPathComponent];
		return [[FileSystemItem alloc] initWithPath:path parent:[FileSystemItem rootItemWithPath:parentPath]];
	}
}

The reason you can't use this to create any item is because you'll end up with duplicates. To use this method, use the original code for D<nowiki/>ataSource, except change     [F<nowiki/>ileSystemItem rootItem] to     [F<nowiki/>ileSystemItem rootItemWithPath:@"/path/to/wherever"] --JediKnil

----

The above code works great for folders that are only one level deep ("/Volumes" "/Library" "/Applications"), but it will not work with deeper directories.  How would one go about doing this?

*Use the     rootItemWithPath: code to initialize your root directory, instead of     initWithPath:parent:*

----

So I changed:
    
+ (FileSystemItem *)volumeAtIndex:(int)index {
	if (volumeDir == nil)
		volumeDir = [[FileSystemItem alloc] initWithPath:[@"~/Library/Application Support/" stringByExpandingTildeInPath] parent:[FileSystemItem rootItem]];
	
	return [volumeDir childAtIndex:(index + 1)]; // +1 because of .DS_STORE. This really shouldn't be hardcoded.
} 

To
    
+ (FileSystemItem *)volumeAtIndex:(int)index {
	if (volumeDir == nil)
		volumeDir = [FileSystemItem rootItemWithPath:[@"~/Library/Application Support/" stringByExpandingTildeInPath]];
	
	return [volumeDir childAtIndex:(index + 1)]; // +1 because of .DS_STORE. This really shouldn't be hardcoded.
} 

Now it 'works' - but doesn't display all of the items in the directory (missing about half the files).  Did I do something incredibly stupid?  Thanks for all the assistance.

*You might have to change numVolumes as well...or maybe you just maxed out your outline view (unlikely, though -- Application Support doesn't have that much in it, I thought). Otherwise, you've got me stumped on this one. And you're welcome...sorry about this one. --JediKnil*

Yeah, I'm pretty sure it has nohing to do with the outline view being maxed out.  I made the numVolumes method uuse the rootItemWithPath method and I recieve a crash on launch (EXEC_BAD_ACCESS).  Hmm...It doesn't make much sense ;).

*Are there any other examples that demonstrate a finder like NSOutlineView?  -- The Apple example is pretty weak�*

