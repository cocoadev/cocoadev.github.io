---
layout: page
title: MetaDataWithSpotLight
---

I'm not sure if I understand exactly what spotlight qualifies as metadata, would it be theretically possible to add arbitrary fields of information to any file, for example could I personally add a rating system like itunes has to all my files then search in terms of "stars" for instance?  Or possibly add a field of who gave me the document, so when I type John Smith I get a list of all the documents I've received from him?

- FranciscoTolmasky

----

Who knows, wait until we get a firm API. There are another 6-12 months of development time ahead of it, and I am hoping they will make it as extensible as feasibly possible without making a complete mess of it. -- MatPeterson

----

I'm actually pretty excited that Apple's even bothering to provide an SDK for this ( and other Tiger things ). Of course, I won't get it until 6 to 12 months from now since I didn't attend WWDC. Frankly, I've been a little miffed at Apple for doing so much really keen stuff in Panther and not giving 3rd party developers ( public, not ClassDump ) access to the APIs. --ShamylZakariya

----

Well, the good part of that is that if they can make an eventual public interface a subset of what's currently in the private framework then we get compatibility back to 10.3.

----

Spotlight is not a simple trick, it's a comprehensive architecture. I don't think backporting is reasonably going to happen by either Apple or a third party.

----

Yeah, no backward compatibility for 10.3. Apple released some more info in the recent days about spotlight.
http://www.appleinsider.com/article.php?id=733
http://developer.apple.com/macosx/tiger/spotlight.html
Check those out. Basically, if you make a plugin you can add any fields of metadata to any file format. For instance, if you made a plugin for safari cache that dug out the site and the title and maybe even the content, you could make it searchable. There are two tools that come with tiger that are pretty nice. One is 'mdls' which displays all the metadata associated with a given file. The other is 'mdfind' which is a powerful tool for searching through the database. Spotlight is kinda picky about what formats it content searches. It will content search mail messages but I haven't found anything else it will do that to. And if you mdls the mail message it doesn't have any of the content so that must be stored somewhere else. I'll find out where it is though...mark my words!

ZacWhite

----
 **Spotlight without files**

How would one implement SpotLight searching for an application that does not use files to store user data but uses, for example, CoreData? Of course one can implement a search field within the application but it would be nice to be included in the global SpotLight search. Is it possible to use the SpotLight API to include data that is not given in the filesystem? -- UL

*Well, actually, CoreData **does** store objects in the filesystem. It just does it automagically.*

A SpotLight plugin for the XML data store should be possible. But for the binary or SQLite format it does not make sense. -- UL

How would it make any more sense for an XML data store than for an SQLite db??

Well, at least it is technically possible to read the XML file and index it with a custom SpotLight importer, which is not easily possible for a file with binary content. In general I agree that it does not make sense to treat a CoreData data store file as a regular file that should be indexed by SpotLight. So there still is no solution to the original problem ... -- UL

You're not making any sense. A CoreData XML file and a CoreData SQLite db are storing the exact same information in different formats. You should be able to write an importer that works for both; in fact, CoreData most likely hides the difference between the two formats in the first place, so that your XML CoreData importer will JustWork with the SQLite format as well. -- MikeAsh

Don't forget that SpotLight indexes files only - so even if you have your database stored as a file, then SpotLight is only going to find the one file! This is why Mail now creates a file for each message.  Hopefully Apple will extend spotlight to either index files
that hold records, or index files that are file systems (i.e. zip, tar, dmg) -- RbrtPntn

----

Seriously - read the links provided above. This answers your question directly. Anything beyond that is NDA until Tiger's official release. I don't even have a copy of this - it's hard not to ask, but all I can do is read the docs linked above. From those docs, I directly infer that anything created by CoreData can be used with Spotlight. Period. In fact, I'm *pretty* sure that's part of the purpose of creating a standard framework like CoreData.

