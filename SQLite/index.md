---
layout: page
title: SQLite
---


SQLite is self-contained, serverless, zero-configuration, transactional SQL database engine. Cocoa does not include any framework wrappers around SQLite, though Core Data can use it as a data store. 

http://www.sqlite.org/



== SQLite Wrappers ==

There are many SQLite wrappers with various advantages.



=== FMDB ===

https://github.com/ccgus/fmdb


=== PLDatabase ===

http://code.google.com/p/pldatabase/

BSD licensed with full unit tests. Intended to support other databases with a JDBC-like interface, though initially focused on SQLite. Should be useful for implementing higher level abstractions, such as an ORM.


=== QuickLite ===

Seems to have died?


=== Objective-C SQL Query Builder ===

The following Objective-C SQLite library offers an easy to use Data Access Object (DAO) and a set of SQL builder classes that make writing queries extremely simple.  The DAO supports multithreading and only requires one line code to execute a query.  The SQL builder classes work similar to those methods used in LINQ, which helps ensure that the SQL statements are well-formed.  It is licensed under the Apache License, v2.0.
https://github.com/ziminji/objective-c-sql-query-builder



=== ActiveRecord === 

ActiveRecord is a very easy to use database framework written by ninja kitten ( http://ninjakitten.us )
It should look very familiar to developers that have worked with rails.

ActiveRecord can be found at http://github.com/aptiva/activerecord/tree



=== SQLite Persistent Objects ===

Here is a project over at Google Code for using SQLite in Cocoa and Cocoa Touch:
http://code.google.com/p/sqlitepersistentobjects/

This is similar to ActiveRecord in that it hides all the implementation details - you do not need to use the SQLite API at all, or write any SQL. You just subclass a class from the library and all the persistence details are handled for you.


=== ITSQLite3Database ===

IThinkSoftware's ITFoundation ( http://source.ithinksw.com/ ) includes a lightweight wrapper over SQLite (ITSQLite3Database) that fetches results immediately, as opposed to using an intermediate "live" result set object, allowing for writes to the database as often as possible between well-formed queries.


=== Foundation Data Objects ===

https://sourceforge.net/projects/fdo is BSD licensed OO database convenience library for SQLite that runs on OS X 10.4 and 10.5 as well as iPhone OS.

