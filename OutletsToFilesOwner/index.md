---
layout: page
title: OutletsToFilesOwner
---

To add an outlet to FilesOwner, first create a class as normal (subclassing whatever is appropriate - NSWindowController perhaps) and then add outlets to it as normal. Then go to the FilesOwner object in the nib and set its custom class (in the info panel) to your class. You should then be able to connect everything up.

For issues releated to using FilesOwner in a document-based application, see also the threaded discussion on ConnectNSDocumentSubclassOutletsViaFilesOwner

