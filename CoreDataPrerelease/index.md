---
layout: page
title: CoreDataPrerelease
---



In an effort to avoid the CocoaDev chaos that came with the introduction of CocoaBindings, and given that Tiger will be released within about a month or so, I've moved the CoreData speculation and pre-release information to this page, leaving the CoreData page itself open for solid info. �DustinVoss
----
CoreData is a new technology in Tiger. The following is the only information that has been made publicly available (as part of the WWDC session descriptions):

----
418 **Introduction to Core Data**

This session provides an overview of the new Core Data framework in Cocoa. It will focus on the new functionality provided for managing and persisting model objects, which includes automatic undo/redo, input validation, and saving to various types of "persistent stores" (SQL and XML).

436 **Advanced Core Data Features**

Learn about the more advanced features of the new Core Data framework, including how to work with multiple persistent stores at the same time, how to use predefined fetch requests and predicates to find your objects, how to get more out of your validation rules, and how to manipulate schemas at runtime.

The following extract hints at it being integrated with Xcode's new modelling stuff (see http://www.apple.com/macosx/tiger/xcode.html ):

313 **Modeling/Design in Xcode**

Want to take your software design skills to the next level? Learn about Xcode's new design tools for object design and persistent object modeling. With these new tools you can view and edit a visual model of your object-oriented code in C++, Objective-C, or Java, and use the model to navigate your source base. Then, create an object graph of your application's object model, and automatically generate a schema for Cocoa's new Persistence Framework.


Here's to hoping that it finally provides a replacement for EnterpriseObjectsFramework!


----

An article on ADC about Core Data (http://developer.apple.com/macosx/tiger/index.html ):

Cocoa has long provided a world-class solution for application user-interface development. And in Panther, Cocoa started providing pre-built controller objects (the "Bindings" layer) to connect various UI elements together with data models. Now in Tiger, Cocoa can manage your data objects themselves through the power of Core Data, providing automatic undo/redo support, additional user interface synchronization, and data consistency, correctness, and speed enhancements when it's time to write to disk.

Core Data gives you the ability to create a description of your data objects. Once defined, Core Data handles most of the heavy work of managing your data objects, both in-memory and on-disk. This allows you to focus on application logic and avoid the infrastructure work. In short, Core Data is a model-driven object graph and persistence framework.

In Tiger, Core Data will support three different kinds of files for storage of data:

- A text-based XML file format

- A better performing binary file format

- A high-performance, SQLite-based database file format

Each of these file formats has its strengths. The XML file format is a good choice during the development of an application as it allows you to peek inside the file and see what is going on. The SQLite format will often be the best choice for desktop applications because of its performance characteristics.

----

NDA?  We don't need no steenkin' NDA.  

* I don't think this page contains any confidential Apple information. So no need to raise NDA issues.* 

----

From a chapter about Core Data published by Apple in the public Tiger overview document.

The Core Data framework (CoreData.framework) is a new Cocoa technology that integrates with Cocoa bindings to make it even easier to create applications based on a Model-View-Controller architecture. Core Data applies to the data portion of your model and helps you persist your data to any number of different storage mediums. A new tool in Xcode lets you graphically create a schema that describes the data entities in your application. Instances of these entities are then managed by the Core Data framework and stored to a storage medium such as an XML file or SQLite database.

By using Core Data to manage your data model, you significantly reduce the amount of infrastructure code you have to write for your application. You no longer have to worry about the problems associated with object lifecycle management. Core Data handles tasks such as retrieving object data from disk, maintaining references to those objects, and writing modified objects back to disk. Core Data also provides the following features: 

- Management of undo/redo beyond basic text editing 

- Support for validation of property values 

- Support for propagating changes and ensuring that the relationships between objects remain consistent 

- Grouping, filtering, and organizing data in memory and transferring those changes to the user interface through Cocoa bindings 

When you combine Core Data with Interface Builder and Cocoa bindings, you have a very powerful way to develop advanced applications rapidly. Core Data implements much of the model and controller portions of your application, Interface Builder supports the creation of your views, and Cocoa bindings provide additional controller support to connect the pieces together.

Core Data is intended for applications that have some amount of structured data to manage. Core Data operates on data that has known attributes and defined relationships to other data in your application. For example, you could use it to manage the contents of a spreadsheet or an object-oriented graphics application, such as Sketch; however, you would probably not use it to manage the bits of a bitmap image. If you are starting to develop a new application, or are planning a significant update to an existing application, you should seriously consider using Core Data. For more information about Core Data, including how to use it in your applications, see Core Data Concepts in Cocoa Design Guidelines Documentation. 

* Where is this magic Cocoa Design Guidelines Documentation of which you speak? I didn't think anything in-depth has been published due to NDA.*

http://developer.apple.com/documentation/Cocoa/DesignGuidelines-date.html#//apple_ref/doc/uid/TP30000416-TP30000556

*I see no Core Data Concepts here.*

Presumably they're in an so-far unpublished version of it

----

From the article "Working with Xcode 2.0" (http://developer.apple.com/macosx/tiger/xcode2.html )
 
**Data Models**

Data models go beyond graphically representing the class hierarchies in your application. Instead, they are a fundamental piece of Core Data, the new object-graph management and persistence framework that makes its debut in Mac OS X Tiger. Object-graph management allows you to work with the data in your application, including full undo and redo support. It also provides a well-defined data integrity infrastructure. Object-graph persistence means that the data in your application is automatically stored to, and then retrieved from, a file on disk.

Very little code is actually required to take advantage of these features. Core Data can use a data model, also known as a schema, to define the data-bearing entities in your application and then manage and store those entities for you. It takes care of all of the details of mapping the data in a persistent store to instances of objects in your application. By creating these models graphically in Xcode, you can quickly and easily define your schema without having to create and edit large XML files or write a lot of tedious boilerplate code.

----
Without breaking any NDAs, I can say that CoreData is not scaleable.   The most robust store CoreData supports is SQLite which is unreasonably slow for any serious data load.  If you're a researcher working with 100s of gigabytes of data, sadly, you will still be using your own store or a better databse than lite.  SQLite provides niether the storing nor the retrieving you'll need, if nothing else at least in terms of speed.  For example, to import a 10 gigabyte csv file with three fields, you'll spend 4 hours alone in btree rebalancing, plus whatever overhead coredata introduces and the parsing and resulting object instantiation time.

*
None of the CoreData docs I've seen imply that it's meant for any kind of massive enterprise-level store. It's for desktop apps. If you want massive data handling, it's a full size RDBMS for you.
*

Now you're right about a full size RDBMS... but I will say this:  Apple DID claim their sqlite store was "scaleable" (and I quote).  Moreover, at a meta level, with xgrid Apple is positioning themselves to be truly useful in the scientific/research community... coredata is an incredible concept -- and Apple has really proven again that they are a class act technologically speaking.  However, by using sqlite they are unnecessarily cripling the utility of an otherwise great technology.

*
I can't find the quote you're referring to. Could you link it? I've found SQLite developers using the term "scaleable" about SQLite. I wouldn't be surprised if CoreData is considered scalable from XML files to SQLite. But I can't find anything that implies CoreData is being as anything beyond desktop use. To quote Apple: "The SQLite format will often be the best choice for desktop applications because of its performance characteristics."
*

*
I'd expect that new back-ends could be added to handle other databases. But I (obviously) don't know whether the CoreData code itself has scaling issues.
*

Again, the issue isn't CoreData so much itself, though EOF does add some heft.  The real problem lies in SQLite... which is not meant for serious heavy lifting.

*..and sqlite is just one possible backend for CoreData.  Maybe in the future Apple (or someone else) will write a backend to a more serious database.*

----

Last october at the O'Reilly conference someone from Apple working on CoreData stated that one can look at EOF (Enterprise Object Framework) to prepare for CoreData.
EOF links:
    
http://developer.apple.com/documentation/LegacyTechnologies/WebObjects/WebObjects_4.5/System/Documentation/Developer/EnterpriseObjects/DevGuide/GuideTOC.html


http://developer.apple.com/documentation/LegacyTechnologies/WebObjects/WebObjects_4.5/System/Documentation/Developer/EnterpriseObjects/EOTools/EOToolsTOC.html 


----

**Networked Applications**

It looks like Core Data is not meant to be used for Networked Applications (ie. network accessible persistent stores/database back ends).  So other than WebObjects, what other competing technologies exist on OS X to produce Cocoa network-based database applications?  I'm looking for something like WebObjects, but with Cocoa - Objective-C instead of Java.

* I don't know that this is a CoreData limitation. It may just be an SQLite issue. *

* I have been studying the documentation on the SyncServices for 10.4, and if I am reading them correctly you can use the SyncServices to sync multiple instances of a CoreData application (each acting as a client) with one TruthDatabase (used by SyncServices as the main database).  By creating your own data stucture, much like Apple has created one for iCal or Address Book, you can have them all sync in the background, just as iCal does.  If anyone knows more about this or has used it I would really like to hear your feedback, as this is a route I was thinking of going when developing a networked CoreData application. *

With X-San and all the networking tech like X-Grid that Apple has, I'd be surprised if it didn't have an awesome API for Networked Applications.
----

Apple has posted WWDC 2005 Core Data sessions descriptions:

** Introduction to Core Data**

Core Data dramatically simplifies development by handling the management of your application's data objects, both in memory and on disk, allowing you to focus on your application's logic. Learn about Core Data's architecture as we walk though code examples and use Xcode's design and modeling tools to build Core Data-based applications.

** Advanced Core Data Usages**

Advanced Core Data techniques will be covered in depth. Learn how to implement multi-store techniques for writing data to multiple places on disk per document, create custom stores, manage object context migration, build models in code, thread Core Data implementations, and much more.

**Core Data Hands On Session**

In this hands-on session, we'll expand on the basics of Core Data by walking you through a series of examples to demonstrate how you can use Core Data to manage data effectively in your application. From data models to Interface Builder bindings to managed object context initialization, you'll discover practical techniques for using this compelling new Tiger technology in your application.

**Cocoa and Core Data Feedback Forum**

Come give us your feedback on Cocoa and its Core Data APIs.

----

This is a link to Apple's new (2005 April 05) "Developing With Core Data" http://developer.apple.com/macosx/tiger/coredata.html

