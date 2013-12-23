---
layout: page
title: BDAlias
---

BDAlias is a simple wrapper class for AliasManager alias records that allows them to be created from NSString file paths and stored as NSData objects.  This is useful if you wish to store references to files that may be moved or renamed in your preferences or in a document.  Alias records are more robust than pure file paths for this use, though alias records typically store a path as either a primary or fallback resolution mechanism along with (depending on the features of the particular filesystem involved and the way the alias record was created) file ID or inode information and relative file information.

Note that BDAlias deals with the *alias record* programming construct and not the *alias file* user construct that users can create and manipulate using the Finder.

BDAlias was written by ChrisHanson and is OpenSourceSoftware under a BSD license.

Originally http://eschatologist.net/bDistributed.com/index.html now http://github.com/rentzsch/bdalias

There is similar class named NDAlias.

