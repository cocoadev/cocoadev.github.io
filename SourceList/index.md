---
layout: page
title: SourceList
---

SourceList is an open-source iLife '08 based NSView subclass hosted at http://code.google.com/p/sourcelist. Let me know here if you use it and I can link you from my site at http://www.33software.com.

SourceList is really easy to use, it exposes three content bindings and one for selection:

* NSContentBinding, this should be bound to the -arrangedObjects property of an NSTreeController or other compatible controller.
* NSContentValuesBinding, these are the item titles and should be set to the appropriate controller collection subpath.
* KDContentImagesBinding, should also be set to the appropriate subpath.
* KDSelectionIndexPathBinding, which unlike NSSelectionIndexPathsBinding only supports a single selection, will typically be bound to the -selectionIndexPath property of the content controller.


SourceList is dependent on another one of my projects Amber.framework which is available at http://code.google.com/p/amber-framework.

http://33software.com/images/components/SourceList.jpg

The screenshot was taken of the included example application which parses the 'iTunes Music Library.xml' file, I'm moving towards using an SAX based parser to improve performance.

