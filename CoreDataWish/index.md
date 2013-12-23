---
layout: page
title: CoreDataWish
---



I like the notion of CoreData. I like the simplicity of the API. I like the Obj-C.

It's sad that one can't substitute another database engine (like mySQL) into the back-end (even by subclassing & overriding all the relevant methods).

*Well, how far along did you get?*

Because the client/server world is where this stuff would REALLY come into it's own.

(In other words I'd love to be able to write a Cocoa app using CoreData, Bindings, etc. that would hit some existing [remote] mySQL databases - databases that are already being used by some other existing client apps).

Nobody currently believes this is possible using CoreData in its present form right?

Correct.  You'd have to do some really heavy lifting using undocumented APIs, and then you'd break on the next OS Rev.  Submit bug reports to apple listing what database you want, and why, so they can prioritize the issue.

*To store the data in a database of choice, it would be necessary only to replace the API that Core Data itself calls to store and retrieve its data. Fortunately this API is not only documented, but comes complete with source code - source code that can be used without restriction since it is in the public domain. It would be hard to ask for more. Of course the API just described is the API for SQLite3. Just edit the routines in SQLite3 to do whatever you want them to do, instead of what they are doing now, build the SQLite3 dynamic library, have CoreData load this version instead of the regular SQLite3 library and call it done.*

----

Do we have any articles on the site describing how to have CoreData (or anything else) load a dynamic library other than the one intended? Would we have to replace the system's libsqlite.dylib (or whatever it's called)?

----
Leopard will have new API that allows you to override the default data storage method.  The Leopard dev samples even contain code showing how this is done.  It still won't be a trivial matter to support another DB, but it'll be possible.

----

Just a dumb question, but if you're referencing something completely external to the Mac OS, to the point of it being a database server somewhere, can you really term it "core" anything?  Core data (in my beginners opinion) is part of the "core" language because its self contained.  The SQLite functionality is now pre-built into XCode and is embedded into the app when its compiled.  *This is not correct -- SQLite is just another dylib on the system.  It's not somehow magically statically linked into Core Data applications.*  A MySQL counterpart would not be the same.  You couldn't embed it into the application itself and couldn't even really guarentee it working without other dependant software.  The role of Core Data as I understand it is for internal data storage as an alternate method of creating the model for an app, not really intended for client/server roles.

*No, "Core" has nothing to do with implementation details such where code or data may be stored. The model here is one of layered functionality. "Core" refers to the minimal set of routines in API needed to support the functionality of the entire API layered above it. As an example, the "Core" of NSArray would be two of its methods: <code>objectAtIndex</code> and <code>count</code>. All the other methods in NSArray can be implemented simply in terms of those two methods.*

I think the idea of adding pre-built database client classes is a great one, but I think it would belong in maybe a counterpart to the WebKit framework (DatabaseKit.framework or something) rather than as part of the "core" like Core Data is.  Maybe I'm misunderstanding what makes something "core" though.

*I suspect the point was to use CoreData as before, but have it flexible enough to plug into a remote MySQL database. No point having two interfaces for one thing.*

----

Unfortunately this is not possible without heavy reverse-engineering and reliance upon undocumented non-public APIs. Core Data is *well documented* as being intended *only* for single-user desktop data storage use. As such, it is geared to bring all the benefits of a relational database to developers for local single-user data storage and nothing more.

*Seems a shame.*

I vote for DatabaseKit.framework! I'd love for something open source and flexible to be available. I'd even contribute to such a project. I'd imagine this being a Cocoa wrapper to ODBC. 

*So what needs to be added to the CoreData interface to make it a suitable database client? I don't really know enough about it.*

See above - nothing *can* be added without it possibly breaking horribly the next time Apple releases an OS update. You *never* rely on undocumented / private APIs (Core Data's innards). It specifically states in the Core Data documentation that it is **not extensible**. If you want database client connectivity, forget about Core Data.

*What does **Apple** need to add to make it a suitable database client? There'd be no point in filing a bug report to **apple** if they turned around and told you to fix it yourself, now, would there? Sorry, forgive my sarcasm.*

You're not getting me. Filing a bug report will have it closed automatically as a "behaves normally". This is **BY DESIGN**. They're not going to tell you what you can do to make it behave how you want because it was never intended to. Filing an enhancement request will do very little because Core Data was never designed to be a multi-user database system and it never will be. I don't know how much more clearly I can put it than this.

*You're not getting it.  Filing a bug report will **not** just automatically have it closed.  It is **always** worthwhile to file bug reports and let Apple know about any issues you're encountering with their technologies or what you'd like to see from them in the future.*

----

**It's not clear why you believe that "Core Data was never designed to be a multi-user database system and it never will be"?  It very clearly has its roots in EOF, and appears to have an architecture that would, should Apple wish, allow for multi-user capabilities.  Even as it stands at the moment, it is (just, with some constraints) possible to successfully use a SQLite store with multiple concurrent users...**

See: http://www.cocoabuilder.com/archive/message/cocoa/2005/6/10/138410

See Also: http://developer.apple.com/documentation/Cocoa/Conceptual/CoreData/Articles/cdFAQ.html#//apple_ref/doc/uid/TP40001802-244739  (specifically the last section called "Change Management")

It's possible but will take massive development to avoid dirtying objects when two users attempt to edit the same object at the same time. **Core Data is not EOF**. If you want enterprise-grade database functionality, use EOF.

--

The first message you cite contains a weak economic argument made by someone not from Apple.  Its basis is weakened still further by the fact that WebObject is now free.  Ironically the post goes on to contradict your assertion from a technical perspective:

*
It's clear from examining the guts of CoreData that it is perfectly capable of working on other types of database back end.
*

It's not clear what is the relevance of the second article you point to (in particular, "Change Management").  That section simply points out a difference in behavior between EOF and Core Data, not a fundamental architectural difference.  The underlying conflict detection mechanism is optimistic locking in both cases.  It's not at all clear what your assertion that "it will take massive development to avoid dirtying objects when two users attempt to edit the same object at the same time" actually means, and there's no suggestion that there's any significant impediment to supporting multi-user database access...

----

*Yeah, that's what I was driving at. Sure, if one wanted to create a third-party database kit, one would not extend CoreData, but if *Apple* wanted to, why would they not simply extend something that's already supported by a lot of Cocoa-based code by now? (Whether Apple would want to or not is another matter...)*

----

There's always GDL2, a GNUstep-affiliated EOF clone. Beware, it's not quite so easy to build. When you've built it though, it's very similar to CoreData except that you can connect to actual databases. An adaptor for postgresql is included. There's an interesting article at http://www.linuxjournal.com/article/7101 . You can design your models with EOModeler; the only thing I miss is that NSArrayController cannot use EOEditingContexts, so bindings are not as easy as they could be.

*It'd be great if someone could maintain a pre-built package of GDL2 so it's easier to play with for evaluation for potential use in projects. After trying several times (spending about twenty minutes each) just trying to build the OmniFrameworks, I gave up and rolled my own solutions. I'm a horribly impatient man .... <shakes head> *

----

Currently, the best Objective-C replacement for EOF seems to be AJRDatabase:

http://sourceforge.net/projects/ajrdatabase/ (for the latest CVS release, recommended)
http://www.raftis.net/~alex/source.html

It also contains a very useful adaptor for postgresql.
This project need some help to be polished, though.

----

 a SQLite database can be shared on a network share  so you can try it out a bit but  I think that for example the undo  of core data is fundamentaly incompatible with multi user use.

----

The EnterpriseObjectsFramework includes automatic undo and redo support as well in its EOEditingContext support; undo is in no way incompatible with multi-user use.  CoreData provides merge policies for dealing with the optimistic locking conflicts that can occur when multiple updates to the same underlying data are attempted, regardless of whether one or many processes is involved in the update.

----

That is nice that the  EnterpriseObjectsFramework has that but is it really useful? Optimistic locking can fail and the more users there are,  the longer the lock takes and the bigger the data you have locked the more "Optimistic" you need to be and you can  only undo as long as you have the lock (optimistic or pessimistic). Imagine the problems you can create with the referential integrity of a database with undoing! or are yuo going to be undoing changes of other users as well? 

----

(Suggestion: Please use the REPLY macro rather than the LINE macro when adding on to a particular subsection, that way the wish-list items stay distinct from discussion of them.)

Optimistic locking conflicts are rare in practice, for most multi-user situations, because many users are typically *not* working on the exact same rows.  Optimistic locking occurs per NSManagedObject or EOEnterpriseObject.  When a conflict is detected, the save of the *entire changed object graph* is aborted, ending the transaction that's saving with a rollback, and letting the caller know that they need to fix up the object graph and what the current values of the offending object are.  The caller can then take appropriate action.

With the built-in undo support in CoreData and the EnterpriseObjectsFramework you're not going to be undoing changes of other users.  You'll only be undoing changes that are located in a particular NSManagedObjectContext or EOEditingContext, which is a local scratchpad and not a direct database connection.  Referential integrity will still certainly be enforced both by the underlying database and by the integrity or validation rules for your data model.

Like I said, optimistic locking isn't a problem in practice, since it's very rare that lots of users are hammering the same row.  Rather than guessing about how this stuff works and then criticizing your incorrect guess, it might be worthwhile to do some research before you continue asserting that it's somehow broken.

----

"Like I said, optimistic locking isn't a problem in practice, since it's very rare that lots of users are hammering the same row."So your guess is that it wil not be a big problem and you say i am guessing??. But  the problem  gets bigger the longer you hold the lock  making the undo not very usefull.So i am right after all dispite your condesending tone. 

----

"**Optimistic** locking" specifically means that the framework -- whether CoreData or the EnterpriseObjectsFramework -- is not taking a record-level lock *internal* to the database and making other clients wait for that lock to be released.  Instead, the framework tracks the values of that record as it was initially retrieved, so if the record does change in the database, the framework will not try to just blindly overwrite it upon a save.  Instead, CoreData will follow whatever merge policy you have specified for your NSManagedObjectContext.  In this way, it's a little bit of a misnomer to call it optimistic locking; it's more correctly referred to as optimistic *concurrency* instead.

With the EnterpriseObjectsFramework, your code will get an appropriate exception if an optimistic concurrency violation is detected.  The exception will contain the new values of the affected records, so you can take whatever correction is required to make the object graph referred to by your EOEditingContext valid to save.

----

I was thinking about it and i was wrong in most applications the undo will work very nice and i appreciate your explanation I do know how optimistic locking or concurrency works somewhat but I am person with a pessimistic nature i think. I am also sorry for my tone and you were right I need to research the technology better before commenting. I am sorry. Also did somebody try it with a shared sqlite file? I am pretty sure you can share sqlite files the locking is very coarse so you can not use it with a lot of users 

----

your wish seemst to come true but with postgresql (even better)
see: http://www.karppinen.fi/baseten/

