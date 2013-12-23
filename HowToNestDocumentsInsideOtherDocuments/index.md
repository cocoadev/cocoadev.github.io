---
layout: page
title: HowToNestDocumentsInsideOtherDocuments
---



* Note: Sorry this is long.  I couldn't think of how to explain it in a shorter post so I tried breaking it up a bit to make it more readable.
 
 

Hey Everyone,
  I'm Just beginning to work on an app that will let me manage a list of songs and files associated with those songs.  I'm still very much a cocoa newbie, but I'm getting the hang of things and making slow but consistent progress. 

See the "Application Description" section at the bottom of this post for details on what I'm trying to do.

So far the MyDocument class in my app uses two classes for managing the song library.

CPSongLibrary - Class for holding the contents of the song library and groups (similar to iTunes 'Playlists') of songs.
CPSongContainer - Class for holding information about the song itself and links to files (.pdf, .doc, .txt, etc...) for the song


So far so good, I've got methods working for adding songs, getting the song count, etc, but now that I'm starting to work on the groups I'm running into an unexpected bump as it relates to another feature I want the app to have.

Normally I would think to just use an instance of NSMutableArray for each group, and let each group retain a reference to a CPSongContainer object.  The problem is this:
I would like to have the abilty for documents in my application to "link" to other documents of the same type and display the linked data as if it were its own. I don't want to import the data since I would like changes in any of the "child documents" to be reflected when the parent document is opened.  How can I have a group list in one document accurately "point" to a song object in a different document?  Wouldn't it be archived as its own document data when the parent document is saved?  Is there any way I can create some kind of intermediate class to use as a bridge between documents?  Has anyone ever tried this (successfully or otherwise?)

I'm sure this is a little hard to understand and I hope its clear what I'm trying to do.  It would be almost like taking iTunes and making it a document based app, where any one document had the ability to display the contents of any number of other document's libraries (including playlists) as if it were part of its own library, and use songs from an external (linked) library in its own playlists.

I know it sounds like way too much trouble to go through & I should just be using a single library like iTunes does, but there really are good reasons for it.  Despite the interface similarities to iTunes the intent & use of this app is very different.  I can go into detail if it would help but I dont want to take up too much time & space here if no one cares to know the details.

I'm wondering if anyone has any thoughts on how to structure my classes so that I can acomplish what I want to acomplish above, but still be able to create groups within each library.

Thanks so much for any insight that might help out a newbie...

CliffPruitt
 
 
 
 
Application Description:
The purpose of the app is to let me manage documents related to collections of songs (song sheet music documents, lyric sheets, keynote/powerpoint files, etc...), and also to make weekly "set lists" of songs based on selections from the library.  Its interface has a lot of similarities to iTunes but the files it manages & the purpose it serves are completely different.

It is document based so multiple libraries of songs would be supported.  Each document will display:

*Master Song Library - Analogous to iTunes master Library
*User Defined Groups of songs (optional per document) - Analogous to iTunes playlists
*Song Details including info & related files
*Lists of songs listed by date (optional per document) - A different type of group with a different purpose

Documents need to be able to "link" to other documents and display their contents as their own including their libraries and their groups.  So a "parent" document linked to three child documents would display its own library and song groups, as well as displaying the library and song groups of all three child documents. This would be analogous to iTunes displaying shared libraries, however they will be "mixed" with the documents root library so you will see all data displayed together as one table view.

The part I'm having trouble figuring out how to tackle:

*The parent document needs to be able to modify / change data in child documents, preferably without opening the child document in its own document window.
*Song groups owned by the parent document need to be able to reference songs from the child document.  If a child document deletes a song, it should be removed from song groups in the parent document the next time the parent document is opened.



Example of Use:
Lets say I have one document called "Standard Library" that contains all of my usual songs that are played on a regular basis. I also have "Christmas Songs" that contain (obviously) all my christmas songs. I may also have documents saved for "Youth Songs", "Childrens Church", "Songs I Hardly Ever Use", etc...

Each document could also have its own "groups" so that I might have groups in my "Standard Library" file for "New Songs", "My Favorites", "Works Great Acoustic", "Needs Full Band", etc...  These would be sort of analagous to iTunes playlists.

Now lets say I want to see all those songs at once and be able to choose from them for my Sunday set lists.  I would like to create a document called "Sunday Rotations" and have it "link" to any of the other documents I want to display.  I may also have another document called "Wednesday Rotations" that link to all the same documents but manages my Wednesday lists.  Each "Parent Document" should read the songs and the song groups from its "child documents".  Each document should also be able to own its own top-level groups that contain references to songs from "child documents".  (Imagine adding a song from a shared iTunes library to one of your own personal playlists to get an idea of what I mean.)

http://www.crayoncowboy.com/sandbox/publicimages/cpruitt/lnkillus.jpg

----

There are a lot of complex cases you'll want to think about before actually implementing this. For instance, what if a user deletes a file, makes changes to the master library, and restores the old file? What if they put the document file on a thumbdrive and edit it on a second computer, then copy it back to the first one afterwards? I've done some similar work in my document based applications, to maintain an address book that exists alongside the actual document. For my purposes, the best solution I found was to keep the contacts data as a single, separate file which is controlled through a singleton controller class. When a document is open, changes to a person object is done through the singleton controller, so any other open document will reflect these changes as well. When a document saves its data, it saves the person objects it is interested in as well. Then, when the document loads, it synchronizes the objects with the controller class, and chooses which object to use if one is newer than the other. In case of a deletion, you can choose to restore the object if it shows up again (since it would not be user friendly for data to disappear from a saved document), or ask the user what to do. To make this work, you'll need a way of comparing revisions between your objects, and a way of resolving conflicts when the user tries to edit data on two separate machines, so make sure you give plenty of thought to handling all of the situations (no matter how rare) you might encounter. -DF

----

Hmmm...  rather complex isn't it? Any chance Apple is planning on adding NSSolveAllMyProblems to the Cocoa frameworks?

Is there any merrit to the idea that I could save, not actual object data, but psudo URL's?  For example save a pair of strings that are something like:

'~/Documents/SongDocs/doc1.ext', 'Song Identifier'

I guess in that case I'd have to have a way of giving each song a unique ID to reference.  I'm more used to working with relational databases than with archiving objects so inter-document data sharing is a whole new ball of wax for me. I'm used to it all being in one big data heap.

I noticed Apple uses an XML format for their library.  Any chance XML might somehow make sharing the data back & forth easier?

Thanks for the thoughts!

CliffPruitt

----

If I understand you correctly, I'm not sure saving just the UID would be a good idea-- going back to one of the examples above, if someone made a change to the item with the UID on a second computer, transfered the document file back to their first computer, the change would not be transfered over. The user would expect that the item is unique, and since he just edited it on the second computer, it would be confusing for him to see the same item on the first computer, without any of his changes. On the other hand, if you think this might be a reasonable behavior for the data you're working with, feel free to do it just like that.

Coming up with a globally unique string is simple, take a look at UniqueFileName. After that, just remember what you're dealing with when you're conceptually thinking of objects. Sometimes you'll be comparing pointers to an object, sometimes you'll be comparing unique strings in an object. Once you're used to thinking in this mindset, everything should start to fall into place. 

As far as XML, well, that's just how you'll be saving the data. Reading and writing to disk is the easy part, it's only when you load the objects into memory that it starts to get complex. :)

-DF

----

Yeah I understand a lot of the issues.  The changes would actually be shown (if I'm thining correctly) since I never want a document to ever save data from a linked document as part of its own archived data.  I just need a way to save indicators or placeholders to objects in other documents so when the parent document is opened it would go through something like:

    
OK I'm opening... I need to unarchive all my saved objects...
OK I see that I have linked documents... I need to open those also but not show document windows...
OK I see that these are the song objects I've saved in myself I need to add them to the display array for the song table view...
Now I need to add all the song objects for all the other (linked) documents to the display array as well...
OK I see I have groups to list in the groups table view I need to check up on all the songs & see if theyre still here...
	OK this song is in my internal library its good...
	OK this song is in my internal library its good...
	OK this song is a reference to a song in doc 3... is it still in doc 3...?
		Yep, its still there so its good...
	OK this song is a reference to a song in doc 4... is it still in doc 4...?
		Oops... its not in doc 4...
			I'm going to add the last saved name I have for that song but gray it out to let the user know that its no longer present.
				or (depending on user preference)
			I'm goign to keep a reference to it in case it comes back but not add it to the table view at all...
Next Group List...


I'm not sure if that makes any sense or not.  Like I said I'm a total newbie, but it seems to me that doing somethign like that would work, even if the document was removed alltogether or located on a shared drive on a network that the computer didn't have access to at the moment.  Its a way to say "If you've got this info available show it, if not just skip over it".  I just havent' the slightest idea how I'd go about storing those placeholder objects. Could I store a unique ID for each document & a unique id for a song & use something like:

    
// _TOTAL_ psudo-code!!!

//doc id = "12345"
//song id = 67890

doc *myDoc = [objectHoldingListOfLinkedDocuments docByDocId:12345];

if (myDoc)
{
	song *mySong = [myDoc songBySongID:67890];

	if(mySong)
	{
		return mySong;
	} else {
		return nil;
	}
}


or is that just completely stupid?

As of now I'm just making it a document based app but skipping the doc linking feature but thinking of the way I personally manage my sheet music (using the Finder at the moment) and the way I use aliases to organize things its a feature I think would benifit me very much.  I dont know if it'd benifit anyone else but since I'm the apps first user I get what I want (provided I can figure it out).

:-)

CliffPruitt

----

It sounds like you're on the right track. In my posts above, I was still thinking along the lines of my original example, where the shared data is stored in a single file instead of linked between all the documents, but if you feel the way you're describing is better for your design, then you should definitely follow that. For the actual implementation, look at the page I mentioned above for code to generate a globally unique string that will work great for a UID. An NSDictionary would be a good place to store objects if you're going to look them up by referencing the UID as a key. Create a basic subclass of NSObject, so you can inherit the UID accessors and isEquals (if you need it) in each object you need to make unique.

For keeping track of documents, I would start by  subclassing NSDocumentController, so that you can override the methods it calls to keep the "Recent Documents" menu updated to find and track new documents. You'll still have to do some of the work yourself, but it's definitely a good place to start.

----

little addendum:  apple offers a UUID in the Core data layer of the os. easily generated in cocoa.

heres how to fish:
go to the ADC reference Library, type: "UUID" into the search field, and hit the enter key.
the second hit gives you a code sample, that with a few minor changes works great.

----

Great info.  Thanks.  As of now I'm still struggling to get the UI to do what I want it to do, (can NSSplitView be any more useless?) but I'm making great headway. I'm really excited about this app.

Thanks for all the help & insight!

Cliff

