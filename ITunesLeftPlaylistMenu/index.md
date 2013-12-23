---
layout: page
title: ITunesLeftPlaylistMenu
---



Are there anyone with information on what class the left/Playlist/iTunes-store menu, in iTunes are? A similair menu can be seen in xtorrent.

http://static.pici.se/pictures/hzuTJDOhY.jpg

Might this just be a NSTableView? I have been trying to find some info on it, but can't seem to find any. Maybe the solution is so simple, no explanation should be needed? :)

----
iTunes is Carbon, so it's not anything in Cocoa. However, using an NSTableView is your best bet to copy the look in Cocoa.

----
Oh, thanks! I'll look into it. :)

----
Wouldn't a NSOutlineView be more fitted for this job since there's a hierarchy? What would be the advantages of using a NSTableView instead? How would the hierarchy be handled?

----
For such things, yes an NSOutlineView would be more appropriate. Since it's a subclass of NSTableView, you get the same things from it's parent class. As they say, it's better to use something that already exists than to reinvent the wheel if you don't have to.

----
Check out the DBInterface Demo [http://www.mere-mortal-software.com/blog/sourcecode.php]

----
I did something like this for an app I've been working on. I used an NSTableView but separate NSViews for each item, instead of NSCells. I did this because I had spinning arrow animation in each row, similar to iTunes. NSCells aren't good for animation (unless someone knows of a way), so I went the NSView route. Works quite nicely.

