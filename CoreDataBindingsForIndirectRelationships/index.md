---
layout: page
title: CoreDataBindingsForIndirectRelationships
---


I am trying to build an iTunes-like data browser for my bibliographic app. 
My app is based on a core data model: book <--->> passage <<--->> topics. The databrowser is made up of three columns, bind to 3 NSArrayController; these controllers are bind to the managedObjectContext; I am using the contentSet binding to link every controller to the one preceding it; this produces the very same behaviour that is observed in iTunes when one selcts a genre, then artist, etc.
Here is where the indirect relationship comes on the way.
My data browser follows this sequence: topic --> author --> book. The column sequenced is obvioulsy mirrored by the array controller sequence:
Topic NSArrayController  - Book NSArrayController - Passage NSArrayController.
And here is the trouble. Of course the relationship between books and topics is only indirect; they are only associated through the passage entity. How do I bind the Book NSArrayController to get only books associated to passages associated to the selected topic of the Topic NSArrayController?
I hope I have been clear enough.
Thanks in advance,
Davide
----
If your relationships are bidirectional, you could use a key path.
For instance, if you have one topic selected called     aTopic, you would get the books with

    NSArray *books = [aTopic valueForKeyPath:@"passages.book"];

(assuming the name of the relationships that goes to the passages in the     topic entity is indeed     passages, and the name of the relationship for the book in the entity     passage is indeed     book). So in terms of bindings, in your nib, that would mean binding the NSArrayController of the books to the topic selection as returned by the NSArrayController for the topics (Bind To: Topics (NSArrayController)...Controller Key: selection...Model Key Path: passages.book). Does that work?
----

Thanks for the suggestion; unluckily it doesn't work. Here is the error log:

2005-09-13 09:51:26.596 BiblioBeta[17825] *** NSRunLoop ignoring exception '[<_NSFaultingMutableSet 0x398b60> addObserver:forKeyPath:options:context:] is not supported. Key path: entry' that raised during posting of delayed perform with target 4e45df0 and selector 'invokeWithTarget:'

(entry is the real name of the book entity; i just used book to make things clearer)

Any idea?

----
I believe that you would want to bind contentObject and contentArrayForMultipleSelection in the Books NSArrayController to a key path like 
<code>selection.@distinctUnionOfArrays.books</code> in the Passages NSArrayController. 

In other words the contents of the Books column derives from Book property of all the passages in the Passsages column. The @distinctUnionOfArrays operator ensures that a book is listed only once even if more than one of its passages are present.
----
I managed to solve the problem, partially, this way: I bind the Entry (that is books) NSArrayController contentSet to selection.passages and the column of my table in the interface to book.authors. Now, this works, but as you said I get duplicates: if more passages of the same book are associated to a topic, the author name gets duplicated in the column. I tried to bind the column to book.@distinctUnionOfArrays.author, but the table stops working and the log says:

2005-09-14 10:53:42.431 BiblioBeta[21410] [<BibliographicEntry 0x3182a0> valueForUndefinedKey:]: this class is not key value coding-compliant for the key @distinctUnionOfArrays.

For the sake of clearness I set up a simple page to show my app structure: http://www.cnomania.it/garage/appreference.htm


Any suggestion?
----
CoreData uses sets, not arrays. Try     @distinctUnionOfSets. --JediKnil
----
In the cocoadev list John Timmer gave me the right hint. @distinctUnionOfSets and @distinctUnionOfArrays are read only, while KVC requires keys to be also able to write. As a solution I considered the option of subclassing NSManagedObject to a method that reads @distinctUnionOfSets and writes (void). Yet I realized that I must think carefull about the way in which data are filtered by the NSArrayController, to make the columns really work. 

Example: If I choose the Topic of "love" in the first column and Shakespeare in the second column, I must makke sure that the third column shows only those works by shakespeare whose passages are associated to love. If I am not careful I could end up showing all the works by shekespeare, so the first column become useless.

I came to the conclusion that I must implement a separate author entity (this also to allow inserting books with more than one author) and find a way to associate topics also to the Entry (which is book) entity, maybe through a fetched property. 
Do you think there is a way, a straight one, to add to the  Entry (book) entity a property that is an array of all the Topics associated to its Passages?
Cheers, DavideBenini

