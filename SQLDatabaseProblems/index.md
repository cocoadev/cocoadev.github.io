---
layout: page
title: SQLDatabaseProblems
---

Having problems with using a SQLite wrapper, SQLDatabase.
I put it into use in a NSTableView protocol based class, on awakfrom nib it opens the database and puts some test entries in it.
Then sets up the columns as the returned SQL columns too, this all works successfully

after it goes thru and loads everything, the SQLDatabase i have in my class called 'database' seems to be closed or something, mPath inside it(containing Database.db file name originally) is then set to Invalid after successfull loading. 
After this, clicking on the table view will result in bus error because it tries to go load data from the database which has unexpectedly closed.

putting breakpoints in yield that only awakfromnib and objectValueForTableColumn get called
the dealloc functions don't seem to get called, nor the DB close function

anybody got ideas? need to see code?

---- 

Can you show the code? I have been using this SQLite wrapper for my project and haven't had a problem with it. 

----

http://zeratul.ath.cx/cocoa/ contains the 2 files i created, everything else is just SQLdatabase & nib connections



----

After a quick look, it looks like:

1) you're not closing the database after executing the database actions. I've always closed the database before exiting the function. In this case, at the end of awakeFromNib. I don't know the implications of opening/closing or leaving the database open in these cases.

2) you're losing the SQLDatabase * database at the end of awakeFromNib. Try placing [database retain] in if([database open]). It looks like what is happening is that upon exit of awakeFromNib database no longer points to anything and then fails in tableView:aTableView objectValueForTableColumn. I am still learning Cocoa memory management, so this may or may not be the culprit, but typically these retain - release things are at the root of my problems.

Philip

----

Rock on! thanks, :) now i'm going to continue on this project, before that, is there any GUI SQLite database editors for OS X? was just wanting to create a quick one so when i start using it in any projects i got a nice little db manager

----

Nothing that I have found that is very good. There is a nice one for Windows. http://www.caesarsoftware.com/bob
There is a quick, dirty, non-fancy one for OSX, too, at http://sqlitebrowser.sourceforge.net.

