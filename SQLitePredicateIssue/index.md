---
layout: page
title: SQLitePredicateIssue
---



I have a functioning app that I've created using Core Data. I've been using XML as by data store and it's all been peachy, save for its performance issues (fetching a couple thousand entries to delete when quitting takes ages). So I figured I'd switch over to SQLite, only I've run into an odd bug.

    
NSFetchRequest *request = [[[NSFetchRequest alloc] init] autorelease];
NSEntityDescription *entity = [NSEntityDescription entityForName:@"Edit" inManagedObjectContext:[self managedObjectContext]];
[request setEntity:entity];
NSPredicate *predicate = [NSPredicate predicateWithFormat:@"article.name == %@ AND mostRecent == TRUE", [self valueForKeyPath:@"article.name"]];
[request setPredicate:predicate];

NSLog(@"article.name is <%@>", [self valueForKeyPath:@"article.name"]);

NSError *error = nil;
NSArray *array = self managedObjectContext] executeFetchRequest:request error:&error];


The [[NSLog prints out the article's name, but the executeFetchRequest results in...

    
An uncaught exception was raised
2006-10-29 23:31:49.302 WikiGuard[6576] unresolved keypath: article.name
2006-10-29 23:31:49.303 WikiGuard[6576] *** Uncaught exception: <NSInternalInconsistencyException> unresolved keypath: article.name


If I remove article.name from the predicate and just search for mostRecent == TRUE, the error pumps out an issue with mostRecent. As I said, this works in an XML data store, but not SQLite. Crazy. Ideas?

-- bradbeattie@gmail.com

----
Kay, fixed my own problem. You can't search on transient attributes, which all the attributes of edits were.

