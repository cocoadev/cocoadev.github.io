---
layout: page
title: MySQL
---

Popular open source SQL database: http://www.mysql.com/

----

**MySQL Licensing Woes for Shareware Apps**

I recently contacted MySQL.com for licensing information (since the information on their site has no pricing and is quite confusing). After more than a week of back-and-forth e-mailing and unreturned phone calls, I finally got to speak to someone who could - I assumed - give me a straight answer. I explained that I am writing a desktop application that I would like to endow with the ability to connect directly to a MySQL database as a client to use it as a document management system. Though database connectivity is not a primary feature, it is a very nice one to have. :-)

I was actually rather shocked to learn that even using their Java client library would cost $299 **per license**. In other words, if my app costs $50, I'd have to charge $349 just for the privilege. I found this rather shocking and disappointing. They have no alternatives available to spread the use of MySQL to small developers for small applications.

As a result, I'm looking for alternative, legal ways of achieving my goal. I'm guessing the use of XMLRPC with an open-source PHP or Perl backend would be legal, right? In other words, my proprietary, commercial application has the ability to hold a dialog with any XMLRPC-enabled system and the system itself could be open-source. That would theoretically get me 'through the door' and achieve my goal, correct? Does anybody have any experience in this? Should I probably put this on its own page? Am I talking through my a$$? :-)

----
I was wondering if you could write a driver plugin that links with MySQL and your app.  License it under the LGPL, which MySQL provides a license exception for, and then that isolates the rest of your app from GPL-infection (for lack of a better word).  It doesn't seem to me like the best spirit of the rule, but if you gave your plugin bundle a nice interface and made it reusable in any app, I think your ass would be covered rather well.

----
No, I think it has to be licensed under GPL, which means that your app does too. Or no?

----
You could also consider PostgreSQL, I believe.

----
So license the plugin under GPL and interface the plugin to the application using TCPIP.
I think that takes care of it.

----
It does not. This is still a violation of the GPL as far as I know.
   ----
   No, it is not a violation. If you are not linking to any glp-d source code, you are fine.

----
If I remember the MySQL license correctly, you only have to purchase a license if your program depends on MySQL. If MySQL is just an add-on, you don't need a license. 
Write one DB driver using SQLite. Then provide optional drivers for PostgreSQL, Oracle, MySQL, etc. under the assumption that the user will already have one of these databases. That is pretty standard for any package.
----

----

**Comparison**

Does anybody know of a good, **recent** comparison between MySQL and PostgreSQL?

----
Not offhand. I think it is usually six of one and half-a-dozen of the other. You have to pay very close attention to DB comparisons. They often do not consider multiple, simultaneous users.

And, then, there is that other thing about MySQL - in the time you have taken to read this page you could have downloaded, installed, and setup MySQL. I don't think any other DB can do that.
----

Further update on MySQL licensing ... http://www.planetmysql.org/kaj/?p=123 ... http://www.betanews.com/article/MySQL_to_Distribute_Commercial_Source_Code_for_Paying_Customers_Only/1186760331

MySQL is quickly becoming **quite** unattractive to any non-open-source third-party developers.

