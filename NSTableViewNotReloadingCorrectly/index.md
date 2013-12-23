---
layout: page
title: NSTableViewNotReloadingCorrectly
---

I am having a really annoying problem with an NSTableView not responding to reloadData.  I'm pretty new at Cocoa development, so bear with me.

So I stumbled across this article http://cocoadevcentral.com/articles/000080.php  discussing binding array controllers to tableviews.  I worked it into my application, and it worked fine.  However, I have another part of my program that also enters data into the array so I formatted it like this:

    
	Author *newAuth;
	newAuth = Author alloc] init];
	[[newAuth properties] setValue:@"first" forKey:@"firstname"];
	[[newAuth properties] setValue:@"last" forKey:@"lastname"];
	[_authors addObject:newAuth];

	[newAuth release];
	[authorsView reloadData];


Ok, so _authors is an array of Author and authorsView is an outlet to the [[NSTableView.  All the code works fine, except [authorsView reloadData] does not refresh the tableview.  The data only appears after I click on the table header (to sort).  This is driving me crazy.

Also, and this may or may not be related, I noticed this in my inspector:

http://davidleblond.dynalias.com/lj/screen_capture_inspector.jpg

Notice the indexes of the 2 tableviews are (,) and ()... is this normal?

Thanks to anyone who can help me!

----

Hello.  I'm not entirely sure what's going on, but I suspect     reloadData is only intended for use with table views that get their data from a dataSource.  I think your problem is that you aren't using indexed accessor methods.  Try

    
	Author *newAuth;
	newAuth = Author alloc] init];
	[[newAuth properties] setValue:@"first" forKey:@"firstname"];
	[[newAuth properties] setValue:@"last" forKey:@"lastname"];
	[self insertObject:newAuth inAuthorsAtIndex:*foo*];

	[newAuth release];


and implement     -[[[YourClass insertObject:inAuthorsAtIndex:].  You might want to go for     -[YourClass countOfAuthors] while you're at it, so you can use that where I wrote *foo*.

The issue is that     [_authors addObject:] is to     -[self insertObject:inAuthorsAtIndex:] as     _authors = newArray is to     [self setAuthors:newArray].  It goes behind the back of the bindings system, and objects which bind to     _authors do not get properly notified.  

Take a look at http://homepage.mac.com/mmalc/CocoaExamples/controllers.html if you have not already.  Mmalc covers this.

Unrelatedly, Apple reserves the underscore prefix for their own private variables.  You want to do something different to avoid name collision.

-KenFerry
----
That did it!  Thanks a ton!

