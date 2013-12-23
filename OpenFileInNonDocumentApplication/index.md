---
layout: page
title: OpenFileInNonDocumentApplication
---

I am working on a non-document based application and I am looking for an alternative to loadDataRepresentation.
When I double-click a file with associated file-tye, the application get focus but what is the method I should implement ? In which instance.. appcontroller ?

----

Does this work:

- (BOOL)application:(NSApplication *)theApplication openFile:(NSString *)filename

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSApplication.html

(In your NSApplication delegate)

----

Thanks, that works fine !

