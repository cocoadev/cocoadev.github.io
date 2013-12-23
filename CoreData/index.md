---
layout: page
title: CoreData
---

The technically correct way to describe Core Data is as an object-graph management and persistence framework. In down-to-earth terms, this means that Core Data organizes the application's model layer into a set of defined in-memory data objects. Core Data tracks changes to these objects and can reverse those changes on demand, such as when a user performs an undo command. Then, when it is time to save changes to your application's data, Core Data takes care of archiving the objects to a persistent store. And, it saves data into regular files that users can manage with the Finder, search with Spotlight(1), backup to CD, and email to friends, family, and coworkers.

(1) Actually my understanding of things was you'd need to write your own spotlight importer for your core data files. Perhaps if you use XML as your deployment file type Spotlight would index those (being plain text XML and all), but if you use Binary or SQL (which I think the majority will) you'd have to write an importer for those. If I'm wrong please link to the docs I'm missing.

http://developer.apple.com/documentation/Cocoa/Conceptual/NSPersistentDocumentTutorial/07_Metadata/chapter_8_section_1.html
and
http://developer.apple.com/documentation/Cocoa/Reference/CoreData_ObjC/Classes/NSPersistentStoreCoord.html

----
Pages related to CoreData:

[Topic]

http://developer.apple.com/macosx/tiger/coredata.html
http://developer.apple.com/documentation/Cocoa/Conceptual/CoreData/Articles/cdTroubleshooting.html
 
----

**Overview & Tutorial**
----

Core Data Class Overview: http://www.cocoadevcentral.com/articles/000086.php
Build a Core Data App: http://www.cocoadevcentral.com/articles/000085.php

*Both with thanks to Scott Stevenson for a job well done.*

Core Data, command line utility: http://developer.apple.com/documentation/Cocoa/Conceptual/CoreDataUtilityTutorial/index.html

At WWDC, Aaron Hillegass made a special presentation to students about Core Data, including slides and hands-on projects.  I don't think this is posted anywhere else.  http://www.bignerdranch.com/wwdc/ (doesn't work 21/11/2008)

Core Data as a Cheap Database: http://www.macgeekery.com/gspot/2005-40/core_data_as_a_cheap_database

----
A philosphical set of questions and answers about Core Data
CoreDataQuestionsAndAnswers

----
FYI boys and girls: CoreData will not scale.  It does not allow you to set the cursor.  That means when you do a giant fetch, if it's really giant in theory you'd want to process the first N objects, then flush them from memory and then process the next batch.  CoreData makes you load the entire query result... which pretty much makes CoreData unusable for any heavy lifting.  Too bad, CoreData could have been fun even for the big boys.
----
This is totally false, as CoreData will allow you to set the limit and offset of the returned data set. The API docs themselves state that a dataset of hundreds of thousands of rows is considered 'small' as far as CoreData programming goes

----
Core Data will scale, and it does not require loading of "the entire query result."  Core Data can create faults to the result of a query or a relationship traversal, and will fire those faults as objects are accessed to populate them.  This technique was also used to good effect in the EnterpriseObjectsFramework.

----

This article has a sample application that shows how to use core data with concurrent threads:

http://www.duckrowing.com/2010/03/11/using-core-data-on-multiple-threads/

