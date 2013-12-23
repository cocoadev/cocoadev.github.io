---
layout: page
title: NSTreeControllerBug
---

I believe I have found a bug in NSTreeController that is seemingly isolated to 10.4.3:

I recently picked up a project I had started a few weeks ago prior to the release of 10.4.3.  Basically, there is a table and an outline -- an array controller and a tree controller.  The table is responsible for a source list of sorts, and then the outline displays stuff inside the selected source item.  This all works well and good... until you do the following:

Let's say there are 3 items in the source list: Item One, Item Two, Item Three.  Item One contains 4 things to be displayed in the outline view, Item Two contains 6 things, and Item Three contains 8 things.  If I select Item One, then Item Two, then Item Three, everything works as fine.  The outline updates with the necessary items.  Now, If instead I select Item Two then Item One, or Item three then Item Two, I get an out of bounds error.  The problem, thus, apparently is caused by updating the tree controller with a lower number of items than the previous selection in the source list.

The error looks something like this:     *** -[NSCFArray objectAtIndex:]: index (4) beyond bounds (4)  The outline properly updates with the items in the selected source list, but it locks up and then crashes if I click anywhere.

I realize the description is kind of vague, but it's a rather specific problem.  If anyone understands what I mean, has an idea as to how to go about fixing it, or has experienced it themselves, please speak up.  Thanks. 

----

I had the same problem and the only workaround I found was to subclass NSTreeController and override setContent: with the following method:

    
- (void)setContent:(id)content
{
	if(![content isEqual:[self content]])
	{
		NSArray *paths = self selectionIndexPaths] retain];
		[super setContent:nil];
		[super setContent:content];
		[self setSelectionIndexPaths:paths];
		[paths release];
	}
}


----

Thanks a bunch for the solution, works like a charm.  Kinda weird that the problem occurred in the first place.   Oh well, it works now -- that's all that matters =).

----
Thanks from me, too, it works perfectly. It also cures the problem that an [[NSTreeController that should "avoid empty selection" sometimes has an empty selection... I highly recommend this hack for anyone using NSTreeController.

----
STELLAR! A project I was working on suddenly starting crashing randomly with the 10.4.3 upgrade... this did the trick for me, too!
What I wanted to add is that my app is set up just like the original example with the selection of an table+array controller driving a tree controller. My app is a Core Data app and binds contentSet to the array controller. Just wanted to confirm that the above fix works for Core Data using contentSet bindings.

----

Warning: This workaround only works if you do not modify the data that NSTreeController manages after you set the content.  If you modify the data in any way after you hand the content to NSTreeController then your application will still crash even with this workaround.  It will appear to 'just work' at first but then your app will crash at some non-deterministic time down the road.  Be sure to do solid regression testing on your application if you attempt to use this workaround.

Detailed discussion of many other alternate workaround attempts that all failed in: NSTreeControllerBugOrDeveloperError  The only workaround that truly works is to design your application so that you can make use of NSTreeController's 'Automatically prepares content' feature.  If you use that feature and provide content to your controller through NSTreeController's own fetch predicate then you can add and delete objects to your model and change their relationships and NSTreeController can handle it just fine when the data changes.  ALL other workaround attempts will lead to the crash if your data changes in any way.  That includes merely altering relationships of existing objects, not just adding and deleting objects.

