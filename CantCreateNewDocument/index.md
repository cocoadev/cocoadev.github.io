---
layout: page
title: CantCreateNewDocument
---




I'm a newbie at Cocoa, although I know other OOP languages (REALbasic, and Java). I'm learning mainly from Aaron Hillegass' book, and also from the Web. I just started my first real project (other than tutorials, etc.). It's a document-based program, and I've created my model class, and a little bit of my NSDocument subclass, as well as a simple view to start with. When it is compiled and runned, I get an error as soon as the app has launched, "Can't create new document" and I also cannot create documents with the menu item.

I've looked over my code multiple times, as well as putting NSLog statements all over the place. None of the NSLogs are called, so I assume there's something wrong fairly deep. It would be great if someone could point me in the general direction of where to look for mistakes.

Thanks

-BenjaminGoldenberg

----

I think I remember having this happen before.  Make sure that the class in your nib matches the one in your Project.  In other words, open the document nib, drag the file "whateverdocument.h" into the nib shower, then click on the file owner, hit command+shift+i for the inspector, select "custom class" and from the list choose "whateverdocument". -FranciscoTolmasky

----

http://www.macdevcenter.com/pub/a/mac/2001/06/01/cocoa.html  --zootbobbalu

----

Also, be sure that the name of the Document Class in the Document Types section of your Target Settings matches the name of your class.  -- Bo

----

Thanks everyone.  It turned out I needed to change the Document Class, as Bo suggested. Also, as this is my first post on CocoaDev, I was wondering what I should do with this discussion now. I know they are supposed to be put in RetiredDiscussions if it is particularly interesting or has good information, (which I don't think this has). Otherwise should they just be deleted? - BenjaminGoldenberg

