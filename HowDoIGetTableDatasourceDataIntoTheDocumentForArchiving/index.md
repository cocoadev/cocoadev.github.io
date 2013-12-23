---
layout: page
title: HowDoIGetTableDatasourceDataIntoTheDocumentForArchiving
---

Please help if you can. I don't have much hair left.

I have a project with a document called iDMDocument, a window controller called iDMController and on my main document window I have a tableview that uses an NSObject called CombatTableDataSource as its data source.

The trouble is that in order to archive the document's data, I first need to sync up the data in the data source (a single array of other objects) with an identical array in my document object. At that point I can use the archiving methods to save the data to a file.

The problem is that I have no idea how to get my iDMDocument to talk to the CombatTableDataSource object. I simply need it to pass the array from the data source to the document.

Can anyone help? I'm desperate.


(As a side note, it was no problem to get the window controller object to pass its data by using self windowControllers] makeObjectsPerformSelector:@selector(prepareForSave)] to call a prepareForSave method in the window controller which then calls a method in [self document] to set the data in a variable in a document.) - this was courtesy of Building Cocoa Applications A Step-by-Step Guide by O'Reilly.


Thanks,
Kent!


ADDENDUM:

I got it!

From inside the window controller, this reference let me access the data source:

[[NSMutableArray *combatants = [self combatTable] dataSource] combatants];

Kent!

----

Uh ... why are these lists separate? Normally your document is also your table's (or tables') data source(s) and you'd just vend your document data directly to the table. The notion of keeping a separate controller w/ separate data that needs to be kept in sync w/ project data sounds [[OverEngineered. 

-- MikeTrent

----

Thanks.  I actually moved EVERYTHING into the document itself.  Now there is no seperate windowcontroller and no seperate datasource.  Much cleaner.

It does lend itself to a very large document object, though.  Oh well.

Kent!

