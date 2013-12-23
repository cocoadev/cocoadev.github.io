---
layout: page
title: FoundationKitLinks
---



Hi,

I looked through the link page for the FoundationKit and noticed 2 things:

a) it must be a pain to do any kind of table-related layout in Wiki. :)
   Perhabs one could use Wiki tags similar to current HTML-Table tags to make
   things a little easier

b) is there any reason for including certain classes (like NSArray ) while 
   excluding others (like NSString ) 
   [obvuoiusly someone else referenced NSString somewhere else already. :)]
   Some of the classes there (like NSGregorianDate) aren't even on the
   FoundationKit description over at AppleComputer
   (Note: It's a good thing (tm) we have them here!)

I would have added some of the missing classes, but I didn't know if they were missing on purpose, and besides there's still that formatting issue...

-- JensBaumeister

----

I'll see if I can think of an easy way to modify the script to do tables like the one on the FoundationKit page.

No, I'm not sure why some classes are missing.  When I created the page, I pretty much just cut-and-paste one of Apple's index pages.  It was pretty late, and I might have screwed it up.  :)  I'll recreate the table, and merge in the mystery new ones like NSGregorianDate. 

You should feel free to add anything that's missing.

-- StevenFrank

----

Okay, I "un-table-ized" FoundationKit by copying all the class names, pasting it into an empty CodeWarrior document, splitting the two columns by clicking between them and pressing return, using Cmd-[ to shift everything to the left-most column, saving, then going to the Mac OS X terminal and running the file through sort, and finally pasting it back into the wiki.  :)

If someone wants to do this for AppKit, that'd be really helpful.

-- StevenFrank

----

You got it. Let me know if I miss anything, of course.

-- RobRix

----

Finished sorting the AppKit, but I haven't added any classes or that because I wasn't exactly sure where things came from - NSAlertPanel, for instance, didn't show up on the AppKit page I looked at, and the Interfaces weren't there either. It had a whole bunch of Protocols which I didn't notice as well, but that's another story.

In any case, it's sorted now - a start, at the least!

-- RobRix

----

Thanks for the AppKit re-formatting.

Now the question is, what do we DO with all those links?  Is there any value in copying the actual docs into the wiki vs. just linking to the existing docs on Apple's site?  I suppose it might be easier to search on the wiki, but that's a lot of work for easy searching.  And we'd have to keep it up to date.  It's probably not worth it.

We could have for each class: a link to Apple's corresponding page of docs, maybe an example code snippet, and any usage anecdotes from people who have run into trouble / unusual things while trying to use the class.  This would be kind of like the PHP documentation ( http://www.php.net/ ) where each page in the function reference can have notes added to it by users.

-- StevenFrank

----

When I requested the AppKit FoundationKit class links, notes on usage, gotchas, etc were what I had in mind not copying the existing Apple Class documentation.

Dave

----

I've added a short note to the FoundationKit and AppKit pages describing that intention, just so nobody goes to the trouble of copying the whole thing from Apple.

Feel free to change the wording or remove it if you don't like it. :)

-- JensBaumeister

----

It would be kind of pointless to copy all that documentation because the last time I checked, large portions were still under the "Documentation Forthcoming" spell. We might link to that documentation, and any other documentation provided, however. And if people were finding the documentation from AppleComputer to be insufficient, they could always add their own.

-- RobRix

----

Because I wrote that before I looked at the AppKit and FoundationKit pages, I didn't see that the note there says exactly what I said.

In any case, I'll throw my support behind that...

-- RobRix

----

Sounds like a good approach to me!

-- StevenFrank

