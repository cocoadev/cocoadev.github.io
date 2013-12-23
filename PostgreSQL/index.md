---
layout: page
title: PostgreSQL
---

**What Is It?**

From their website: PostgreSQL is a sophisticated Object-Relational DBMS, supporting almost all SQL constructs, including subselects, transactions, and user-defined types and functions. It is the most advanced open-source database available anywhere.

For more information, you can find a web mirror from http://www.postgresql.org/

----

**Mac OS X Support**

Druware (Andrew Satori) has done us a service by creating an easy-to-use all-in-one installer package for Mac OS X that is known to work on Tiger. It can be found here: http://sourceforge.net/projects/pgsqlformac/

----
I've been tinkering with PostgreSQL on OSX for nearly 4 years now. I've never used it in a production environment, but I've had no trouble compiling it from source and using it on personal projects. --TimHart

----

**Objective-C Wrappers**

Does anybody know of an Objective-C wrapper for connecting to / manipulating PostgreSQL as a client? I know there's a JDBC driver, but I would rather not incur the overhead of Java to talk directly to PostgreSQL. Barring that, what are some of the steps I would need to take to build one myself?

----
PostgreSQL comes with a client library called libpq. It's a very minimal library, very straightforward to use. I've written several Objective-C wrappers on top of it. The each pretty much consisted of taking an NSString, getting its UTF8String, and sending that into the libpq functions.

Take a look at http://www.postgresql.org/docs/8.0/static/libpq.html

----
Thanks for the info! That's at least a good, solid starting point. Let me ask you this, would you be willing to post at least some of your wrapper's source to the wiki for us all to enjoy? If not, I at least thank you for the pointer to the lib.

----
Unfortunately, all the wrappers are owned by clients. In my copious free time, I'm working on a way to make CoreData work with PostgreSQL. If I ever get it in a usable state, I'll post it and you can glean some info from that.

PostgreSQL is a wonderful database. I've used it in several production environments with big, full-featured Cocoa apps as collaborative database-editing clients. Works extremely well. It's even got Bonjour support, so the clients can find the server without the user entering an IP address. Thanks to NSString and PostgreSQL's excellent UNICODE capabilities, it's great that my apps can support any character in the world that users throw at it. It goes over really well at international trade shows.

----
So for my own endeavors, I have two very basic questions after having taken a look at libpq. One, am I correct in assuming I'll have to use a timer to set up polling? Two, should each 'action' (I hesitate to call it a 'transaction' since that has a somewhat different meaning) follow a "connect, do your thing, disconnect" pattern or should I remain connected as long as I intend to use the database?

----
For polling, and NSTimer would work, but you have other options. Check out the 'NOTIFY' and 'LISTEN' SQL extensions. At times polling is an easier solution, but investigate those before you decide. Regarding connections - in many dB applications, its more 'correct' to maintain the connection throughout the life of the application. Building a connection can take several seconds. As always, there are exceptions, and only you know exactly what you want to do with the database. --TimHart

----

**pgsqlcocoa Project**

I'm considering starting a PostgreSQL-based project and I'm doing some preliminary research. Does anyone have any experience with the framework at http://gborg.postgresql.org/project/pgsqlcocoa ? I'm trying to decide whether to start with that or figure out how to make my own wrapper. Opinions are welcome.

----

**BaseTen**

It's probably worth mentioning the BaseTen project on this page: http://www.karppinen.fi/baseten/

It's a CoreData-like interface to PostgreSQL and looks to be good and at least free for non-commercial use, with good documentation. Haven't tried it myself though.

----

**Embedding a PostgreSQL Server in your application**

If you need to embed an SQL server in your app and SQLite won't do (which admittedly is unlikely!) take a look at my project PostgresKit. It's here: 

http://code.google.com/p/postgres-kit/

There's an example of embedding a PostgreSQL server in a PreferencePane, with backups, etc. Might be of some use to you!

Thanks - DavidThorpe

