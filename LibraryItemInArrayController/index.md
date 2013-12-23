---
layout: page
title: LibraryItemInArrayController
---

Hey all.  This was a difficult page to name, so feel free to change it if you feel it better suites what I am going to describe below.

I have a CoreData single-document app which uses CocoaBindings for the user interface.  On the left I have a list of folders, on the right I have a list of items.  I have two array controllers, one that is bound to ALL of the items, and one that is bound to the items in the selected folder.  When the user selects a folder in the folder list, it displays the items in that folder, great.  But, I want this to function somewhat like iTunes (where each item really exists in a master library).  I am completely clueless as to how to go about telling my folder array controller to include a library item at the top, and to tell the item list to bind to the master list of items.  I briefly experimented with manually providing the data source, but it quickly became a hack, and I was duplicating functionality that NSArrayController takes care of.  I would �really� appreciate a nudge in the right direction, thanks.

*What's bound to what? Be specific.*

I have three array controllers:

Selected Items,
Items,
Folders

Folders is bound to my folder table.  Selected items is bound to the selected Folder's items.  Items is bound to all of the items in the library.

*Look at the CoreRecipies sample. It shows how to have a library and smart groups. Dumb groups are left as an exercise for the reader'.*

