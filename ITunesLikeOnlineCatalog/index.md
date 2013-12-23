---
layout: page
title: ITunesLikeOnlineCatalog
---

Does anyone have any ideas on how to create an iTunes Music Store-like online catalog for an application? Nothing nearly as fancy at this point, however. I know iTunes uses WebObjects, but I don't need that big of a system. Just a basic listing of items. I want to user to be able to upload and download items to and from the catalog. I thought about just serving XML files from a web server so the app downloads them when the user access the catalog, but then the issue of multiple edits on the file came up. I need some kind of server, probably. MySQL isn't the best known to work with Cocoa. Is there any easy to implement server database that I can use for this? --LoganCollins

----

You wouldn't be using Cocoa on the server (right?), so any MySQL/Cocoa interactions wouldn't matter anyway. I would use the database of your choice (MySQL, Postgres, whatever) plus the CGI language of your choice on the server side, and have them serve something you can easily parse and display in your Cocoa app.

----

No, :) I just wanted an idea of something that had a history of being easy to implement with Cocoa. i.e. the SMySQL framework that seems to be the best for MySQL is not up to date and has a complicated documentation. I only wanted to know if someone had had experience with a good database to use to download information into a Cocoa app. XML would be perfect since Cocoa supports it natively, but I could get an SQL server to parse to anything, really, I guess.

