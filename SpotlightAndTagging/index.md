---
layout: page
title: SpotlightAndTagging
---



I am not entirely sure what it is I need to ask, so instead I will describe what I want to do and list my possible solutions with their various issues. There are a few questions mingled in here, plus a general plea for advice - I hope this doesn't break the rules of the wiki.

Once upon a time I was forced to work under Windows. While there were very few applications I used there that I now miss, there was one - TortoiseCVS (and its sister, TortoiseSVN). I have yet to find a similar beast for Mac OS X, so I finally decided to hunker down and write it myself. While I program on OS X, I don't write much code for the Mac, so I am a bit inexperienced with Cocoa and what is/isn't possible (or at least relatively easy to accomplish in my spare time). My plan was to write a little app that would monitor a collection of folders under Subversion control using FSEvents (all this is targeted at Leopard only). The app would scan the folder and add custom metadata tags to the various files, like SVN Status and SVN Last Commit Revision. Then you could have a smart folder that showed all files whose SVN status was modified, or do searches for all files committed since revision #x. Yeah, you can do all that from the command line and various SVN apps, but I really like having access to that information from the Finder. 

Of course, those familiar with metadata on OS X will see the flaw in my plan - Spotlight is not a metadata storage system. It's a metadata query system that works by caching existing metadata - there is no facility to create new metadata for an item. *sigh*

So, instead of giving up (ok, I did briefly surrender, but I just can't seem to admit that Windows could have a better interface for a task I need to do a lot) I looked into various options. Here they are:

1. Write a custom importer for SpotlIght. The problem is, importers work by file type - you can't set a specific importer for a given file. Also, only a single importer will be called by Spotlight. Since any type of file can be stored under Subversion, I would have to write an importer that replaced all the existing importers. While I haven't tried this yet, it looks like someone else has given it a shot with SpotMeta. To make this work, the new global importer must call any other importers it supplanted so as not to lose all the existing file metadata. While that appears to be possible, I think it depends on undocumented behavior by Spotlight. It also sounds like a lot of work.

2. Use extended attributes (xattr) of the file. Nice idea and works really well. It's very easy to add, modify and delete custom metadata to any file. Hurrah! But, Spotlight doesn't actually look at the extended attributes for metadata. It just uses whatever importers are installed. Rats. So, to make this work we are back to item #1.

3. Use the Finder/Spotlight Comment field. This is pretty hacky, as these aren't really comments. If you are careful, this works and you can avoid deleting any comments on the file. You do lose some interesting custom searches, like "all files committed since revision 230". You can just look for the presence of some data. Still, it works nicely for things like status (e.g. "all files whose status is modified or conflicted"). I have actually implemented this, just to discover that spotlight caching of Finder comments is flaky. Sometimes they get cached, sometimes they don't. I set the comment using the Cocoa Scripting bridge to the Finder, but the modified comment frequently doesn't make it to Spotlights cache. I have even tried doing an explicit mdimport after the update, but that doesn't help. The weird thing is, if I do 'mdimport -d 4 somefile' I see the new comment text in the debug output, but immediately after mdls still shows the old text and any searches fail to notice the new text. When I look in the Finder, the text is correctly changed, it's just that spotlight doesn't notice it. I have seen similar behavior when editing the comment directly in the Finder. Does anyone have any incite into why that might happen (and how to fix it)?

4. Use labels to indicate status. This loses the ability to search for anything other then status, and I lose a most of my available Finder labels in the process. On the plus side, I can see the file status in normal Finder windows. I haven't tried this yet, but it seems like it should work.

5. Use the undocumented setAttribute:forKey: on NSMetadataItem. I haven't looked closely yet, but apparently it is possible to set arbitrary metadata in Spotlight on Leopard, though it is entirely undocumented. Seems wrong, but perhaps it would work without too much effort (certainly easier then writing a custom importer). One issue I have stumbled on - you can't seem to get an NSMetadataItem from a file path. They are only created as the result of a search (and you can't search for the full path of an item). MDItemRefs can be created for a specific file, but I don't know if there is an undocumented Set function for them. If they were toll-free bridged to NSMetadataItem it would be fine, but I don't think they are bridged. So, if I went this route, how would I find the NSMetadataItem for file 'foo'? Or can I use MDItemRefs?

6. Something else. Any thoughts? What am I missing? Is there some clever way to make this work I have overlooked?

7. Give up. I am on a fools errand and should just live with what I have and hope that Apple eventually provides a mechanism for setting arbitrary metadata that Spotlight understands.

Thoughts/comments/suggestions?

----

I'm inclined to think the unlucky number 7 is the best course for your particular requirements. Why must it be in Finder? What's wrong with a specific application that uses existing, non-hacky interfaces to consistent stores that work as they were designed to, then writing your own well-designed interface around it? Most of the hard stuff is already done for you. If not for your requirement that this be searchable via Spotlight with Finder, your only problem would be to design the UI for a good, easy-to-use repository client.

That said, if you took that approach, you could still easily leverage Spotlight-like features such as Smart Folders using Core Data and an in-memory store. All you'd need to persist is the predicate assembly for the search parameters. The rest is "query the repository the usual way, parse the results into a Core Data managed object context (with in-memory store), hand it to all my controllers for display, filtration, and perusal".

----
I think #7 is your best bet in the sense that you're trying to force Spotlight into a role for which it's not currently intended. I'm sure Apple is well aware that lots of developers would like to be able to add Spotlight searchable tags to arbitrary files, but there are good performance-related reasons that they don't currently do this.

Rather than force a square peg into a round hole, I think you should look for more promising alternatives. AppleScript or Automator folder actions might be useful for what you're trying to accomplish. Or you could write an app that puts a simple GUI front end on svn command line tools and set it up as a user interface element so that even though it's really a separate app, it appears to be integrated into the desktop (similar to the Dock, Dashboard, Expos�, etc.). -CS

----

http://code.google.com/p/macfuse/ + http://fuse.sourceforge.net/wiki/index.php/VersioningFileSystems ???

----

MDItemSetAttribute will set attributes in the spotlight database: BUT don't use it (or the setAttribute:forKey as it is almost certainly the same thing). There are two problems with MDItemSetAttribute - one is that the spotlight datbase can be (should be if you like speedy searches) wiped out with a spotlight rebuild. Then your tags will not go back in. Number two is a little more sneaky. It seems that when you call MDItemSetAttribute on a file for an attribute, then any subsequent mdimport on that file will NOT update the spotlight DB for that key. Your MDItemSetAttribute call has somehow marked that field as 'not to be changed' by importers. The comment above where Finder comments were not picked up by spotlight could have easily happened when using MDItemSetAttribute on that file. In short MDItemSetAttribute will hose the spotlight DB on the computer - unless you use it (I would guess) to add your own custom fields. 

If you want to use spotlight comments applescript is about 100x slower than using an AppleEvent directly to the Finder. The ones in the Apple Sample Code 'More Apple Events' (which is kinda old) works well. - And oh yeah - when you set a few hundred comments with AppleEvents or Apple scripts, the Finder will hang. So you have to time all the calls, and give up when you see the Finder taking more than say .3 seconds to handle an AppleEvent. Then wait for a minute and try again. Fun. --Tom Anderen

----

At least on Leopard, Finder comments (and others, but I haven't checked what else) are stored in xattrs. See for yourself with xattr -l. So the above comment about the supposed dangers of MDItemSetAttribute seems overblown. As far as I'm concerned MDItemSetAttribute is safe, or at least as safe as any undocumented API can be.

