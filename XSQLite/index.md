---
layout: page
title: XSQLite
---

A Cocoa app currently under development by General/JeremyK that is just a nice native OS X GUI for General/SQLite databases.

http://zeratul.asap.um.maine.edu/cgi-bin/index.pl?General/XSQLite

Currently was working out General/NSDocumentProblems (now figured that out) and problems with including General/SQLite library with the program itself, solution was a framework someone made, this was discussed in General/LinkingLibrariesIntoACocoaApp and I'm currently taking a look at General/FMSqlite

http://zeratul.ath.cx/ hosts a wiki with information about projects I've worked on, including this one.  one of the projects is actually a re-development of this particular wiki software.  Currently my site uses the same software with a few small modifications (made URL detector look for &, and the image embedding stuff for png too).  Hoping to find a better style sometime soon and working on a newer version of the wiki done in PHP (I like PHP, and like not using cgi i guess)
----
Update - 10-23-03 12:21AM EST
I posted a sample of my program I'm currently working on, General/XSQLite, it's just the DB viewing functionality so far, only thing that works is the list of tables (clicking on them loads the table's contents in content tab), Query & Structure are unimplemented.

Any feedback is welcome, but don't expect much since it's still heavily under development(open up myTest.db for a sample of how it works)
http://zeratul.asap.um.maine.edu/cocoa/General/XSQLiteViewer.dmg (Viewer meaning it's just working to the point of viewing table data)

**REQUIRES:** sqlite library installed, /usr/local/lib/libsqlite.a , this will be fixed when I figure out General/FMSqlite (see General/LinkingLibrariesIntoACocoaApp )

**WARNING** Wouldn't open a large table in this, even though I'd love that tested, it's not ready for that since it queries the table like mad when you click around(the table get object function fires off a lot)

Also, I've still had very little experience with General/SQLite so I don't know what data types it officially supports and the results it has when loading various column types, but should be ok since I ask for string value for each for now I believe.

Related pages: General/JeremyK , General/NSDocumentProblems, General/LinkingLibrariesIntoACocoaApp
