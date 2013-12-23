---
layout: page
title: ReverseEnginnerCoreDataModelFromSQLiteFile
---

Before opening a 'CoreData' file (and I am more specifically interested in an SQLite file), you need to provide a NSManagedObjectModel, to get a NSManagedObjectContext, which can then get some data from files. What if you have an SQLite file that was saved using CoreData (not by an application you wrote, obiously), and just want to read it and get all the NSManagedObjectModel from it, with the entities and their connection and stuff? Has anyone figured that out yet? Maybe it is very simple for people familiar with SQLite? Thanks for your insights :-)

(NB: I realize that some of the classes in the CoreData application might be NSManagedObject subclasses, with other ivars etc..., but at least I could get the basic structure with just the CoreData compatible properties and relationships)

(addendum: arghh... stupid typo in the name of the page... not gonna change that!)

----
Uhm, not quite sure if this is what you had in mind, but you can open the file in sqlite3 and then use the .schema command to get the sql commands that were used to build then entire db in sql.

So for example, the BlogDemo tutorial from cocoadevcentral has the following sql structure that was auto created by core data

    
Zeus% sqlite3 BlogDemo.bgdoc 
SQLite version 3.1.3
Enter ".help" for instructions
sqlite> .schema
CREATE TABLE ZAUTHOR ( Z_ENT INTEGER, Z_PK INTEGER PRIMARY KEY, Z_OPT INTEGER, ZEMAIL VARCHAR, ZNAME VARCHAR );
CREATE TABLE ZPOST ( Z_ENT INTEGER, Z_PK INTEGER PRIMARY KEY, Z_OPT INTEGER, ZBODY VARCHAR, ZTITLE VARCHAR, ZCREATEDON TIMESTAMP, ZAUTHOR INTEGER, ZTOPIC INTEGER );
CREATE TABLE ZTOPIC ( Z_ENT INTEGER, Z_PK INTEGER PRIMARY KEY, Z_OPT INTEGER, ZTITLE VARCHAR );
CREATE TABLE Z_METADATA (Z_VERSION INTEGER PRIMARY KEY, Z_UUID VARCHAR(255), Z_PLIST BLOB);
CREATE TABLE Z_PRIMARYKEY (Z_ENT INTEGER PRIMARY KEY, Z_NAME VARCHAR, Z_SUPER INTEGER, Z_MAX INTEGER);
CREATE INDEX ZPOST_ZAUTHOR_INDEX ON ZPOST (ZAUTHOR);
CREATE INDEX ZPOST_ZTOPIC_INDEX ON ZPOST (ZTOPIC);
sqlite> 


So what that looks like is an authors table (ZAUTHOR), with a name, email, and some relaltionship keys, a posts table with the same sort of stuff, a topics table with again, the same sort of stuff, a core data metadata table, and primary keys table (prolly to keep unique primary keys, since the other tables don't do autoincrementing) and then some foriegn key indexes.
You could possibly figure out what each of those fields did for some other app too.

 Note that some apps may be able to encrypt the file before it goes out (I've heard of this), which would make this difficult.

----
Thanks very much. Yes, as a complete SQLite newbie, I was not really able to get started anywhere. You saved me 2-3 hours of googling and reading. Of course, I will still need to get more familiar with SQLite... So, it seems also that all entitie names are prepended with a 'Z' (!!??), and that some special types are used by CoreData, Z_ENT, Z_PK and Z_OPT, to index entries for one entity, and the relationships are then defined using an INTEGER for the corresponding column. It could be nice to know the details, e.g. to read the database from another platform.

----
There is an interesting article close to this topic on Big Nerd Ranch:
http://weblog.bignerdranch.com/?p=12

