---
layout: page
title: DifferentIconsForSameClassAndNib
---

Is it possible to use the same class and just one nib file (file owner) with different icons?
My application handles two type of files almost 100% the same. 
However, they should have a different extension and different icons.
What is the easiest way to go?
----

*Double click on the target of the document based app in "Groups & Files".
*Select the Properties tab in the Target Info.
*Add a document type and set the "Class" and "Extension" to the other file type (the class will be the same)
*Set the rest of fields of the new document type to same values of the other document type.
*Now all you have to do is specify the "Icon File" you would like for Launch Services to use for the two different file extensions (don't forget to add the two icon files to the target of the project).


--zootbobbalu
----
Ok,this is what I needed to know. I see that I also get a popup when I save the document now to choose between the types.
Thank you very much!

