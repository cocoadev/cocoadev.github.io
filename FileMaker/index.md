---
layout: page
title: FileMaker
---

So, I'm supposed to write about FileMaker... Oh, dear. Um, FileMaker is a database application, written by Claris Inc (originally, now renamed FileMaker, Inc), Apple's wholly-owned subsidiary.
FileMaker has been a very intuitive, simple database solution, where you can define fields (text, numeric, date, time, container, etc.), scripts and layouts, though not tables: one database file may only contain one table. [This is no longer true as of Version 7! One file can contain multiple tables. Version 7 isn't compatible with earlier versions, though it can convert earlier databases into the new format. However, you can't convert your old databases into one database of many tables, unfortunately.] One database can reference another: it can have one-to-one or one-to-many relationships to it, and run each other's scripts.
You can run scripts by selecting them from the Script menu, or by pushing buttons you define as part of your layout.

If you want to develop very basic desktop database solutions (categorize information, prepare and print reports, do some basic text editing), FileMaker can be a great solution, letting you develop your database, complete with its graphical user interface, in hours or even minutes. FileMaker databases are multi-user and cross-platform: the same files can be opened, even simultaneously, from Macs and PCs. Using a FileMaker database requires FileMaker on each user's desktop. 

FileMaker comes in several flavors. FileMaker Pro is the basic edition (I think). For multi-user purposes, it's (probably) better to also use FileMaker Server as a database server; otherwise the first user to open a database file would become its host. The Server version creates a dedicated host instead, (probably) optimizing performance. Never tried.

Since version 6, FileMaker has a Developer edition that lets you create single-platform, single user versions of your databases that can be run without FileMaker. However, this is a bit of a kludge. All it does is save money... Or lets you pass off your FileMaker databases as real commercial applications, which they will never be. 

The technology has some serious limits. Performance scales badly, it's nowhere close to an SQL server's. It doesn't support SQL either. However, its scripts and calculated fields, and especially its layout features make FileMaker more than just a database solution: it's almost like a database, and almost like a programming tool. It's weak as either, but development is incredibly fast. Up to a certain level, it's probably peerless.

That's it from me.
-- Andr�s Puiz

ChrisEccles

I've been to FileMaker land.
It's a little like Oz.

You're never quite certain what's real and what's a figment of the imagination of
the bloke who wrote the user manuals.

And, with FileMaker, you KNOW you're definitely not in Kansas anymore ...................................

