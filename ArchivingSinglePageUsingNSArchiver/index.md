---
layout: page
title: ArchivingSinglePageUsingNSArchiver
---

Hi all,

I am working on a Editor (Cocoa Document Based application). Each document contains mutiple pages (mutable array of pages). I am using NSArchiver to  archive these pages.  I have to support saving a single page. For the purpose i need to archive only the current page. If the current page is already archived it should be overwritten (without modifying other pages content). Otherwise if it is a new page, the archive data should be appended to the existing archives of other pages. Is there a way to do this?

Thanks
:-)

----

You could save each page as a seperate file in a bundle that you treat as a document.

I'd suggest using CoreData for this bit of abstraction instead.

----
Ok. I will save each page in a seperate file. Is there a way to  serialize each page in seperate parts i.e Suppose there are 2 images on the page. When saving the page, is it possible to save the page properties (such as width, height, background color etc) to a file and then save the images into same file. (only if the page properties are successfully saved)

I am developing on Panther. So using Core Data is not possible. :-)
----

If you're developing on Panther NSKeyedArchiver is the most robust & flexible Persistence solution in Cocoa.
Before you start, read this: <http://developer.apple.com/documentation/Cocoa/Conceptual/Archiving/Archiving.pdf>

