---
layout: page
title: NSMutableArrayVsSQLiteDatabase
---



I have a Dictionary app. Currently it uses SQLite to store 190.000 word pairs (the database file is about 9MB). It uses little memory and searches fast enough *but* the database file can be opened by any other app, because it stores everything in its header (the table names, fields etc.), so anyone can write a reader app for it, and sell it cheaper than I can.

I've been thinking about another solution. Store the words in an NSMutableArray of NSArrays and write them to disk with NSArchiver.
The results are a bit disappointing. It uses twice as much memory as before (now about 60 Megs), starts up two times slower. The only advantage is that it searches a bit faster.

Now what? Sacrifice the memory or give a chance to other developers in my small country to easily reuse my database?
I know all about copyright things, but you never can tell.

----

Well, if your only concern is that the data is in a readable format, then I'd say 1) re-consider if this is not actually a feature, since it allows end-user innovation (the chance of a person buying your application and finding another use for the data is probably larger than the chance of someone else writing and selling an application including your data) and 2) do you have the (exclusive) rights to the data? or is the word/definition list already in the public domain?

--AllanOdgaard

----

I've had a basic database, and some friends of mine helps expanding it (by 5000 word-pairs in a month) because it was quite incomplete. I think after this we can consider regarding it as ours.

----

I was thinking about the same issue, but more because of general security. I'm developing a buisness app, and want to let the user password protect the files. The data format for the app is an SQLite DB, but I'm storing it in a bundle with some other resources. I'd like to encrypt the whole bundle, which would take care of securing the DB, but I'm not sure how to do this, outside of making it a disk image (which would be ugly if it showed up in the finder)

----

Because no-one can extract the encryption key from your application? ;)

Or was that a user password you'd use for encryption? How much data do you have? Encrypting the whole bundle sounds like a bad idea. Why is the data sensitive? I.e. whom are you protecting it from (by default only the user and system administrator will be able to read the users own files)?

----

The data would be encrypted with a user password. It's necessary mostly because the market asks for it. The data might be sent via email, or stored on a CD, or posted to an internal website - it could be stolen. Obviously if the user knew how to use signed email, and encrypted disk images, the application would not need to encrypt the data, but I'm trying to make the app easy to use, and competing applications have the same feature.

----

A friend of mine suggested that before I build up the database, encode the text it will contain (eg. xor the ascii value of the characters with a number). If I want higher security, encode it twice with different algorithms. Then the application can use these algorithms to fetch data from the database. After this, no one can use my database or build a wrapper app for it, because it will only contain hodge-podges.

----

Sounds like it would be trivial for an attacker to reverse-engineer your database obfuscation algorithms.

