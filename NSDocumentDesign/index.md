---
layout: page
title: NSDocumentDesign
---

I asked this question at cocoa-dev@lists.apple.com but I got no reply... I am still working on the problem, but any help would be greatly appreciated.

Here it is: I am working on an application (document-based) and I want to create/initialize a datastructure every single time a new document is created (as a part of the newly created MyDocument object). Mind you, that  data structure should not be touched when somebody simple wants to open a document, i.e. overriding

- (void) awakeFromNib; 

won't solve my problem (furthermore that is executed only once, I think).

Please reply either here or at hristo_n@denison.edu

Nick 

----

This is a bit of a RTFM question, really, but here it goes anyhow.

You need to **initialise** data as a part of your **NSDocument**, whenever you create one.  To do this, you need to override the designated initialiser of your document.  Now, a glance at the NSDocument documentation should show you that there are two designated initialisers for an NSDocument, viz:


*
- (id) init;
*
- (id) initWithContentsOfFile:ofType:;


The first of these is the designated initialiser for new documents - here you would carry out any stuff that's specific to new documents, such as the initialisation you want to do.  The second is for initialising from an existing file.  As you can see, (and as usual) the Cocoa frameworks have done much of the work for you.

So, you need to override both designated initialisers to do what you want to do in the  two cases given, and all will be well.

You may also find it useful to move the shared bits of initialisation code to a separate method, to cut down on Rape And Paste programming.

Alternatively, you might override init only, and rely on initWithContentsOfFile:type: to overwrite your initialised data with the contents of the file. This is marginally more expensive when opening documents, as you are redundantly initialising the data twice (once to the default, and once to the contents of the file) but it shouldn't be a noticable hit.  After all, if initialising this data is really expensive, you should probably be doing it lazily anyhow.

The choice, as ever, is yours.

Oh, and by the way, awakeFromNib is only ever called for objects archived in a nib file.  You didn't instantiate your document class in a nib, did you?
----
The above claim above is incorrect. -awakeFromNib is called for all objects in the loaded nib file AND for the file's owner specified when the nib is loaded.  Whenever an NSDocument instance is used as the file's owner for a loaded nib, the NSDocument instance's -awakeFromNib will be called.
----

The bits of documentation to read are as follows:

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSDocument_index.html
*
http://developer.apple.com/documentation/Cocoa/Conceptual/Documents/index.html#//apple_ref/doc/uid/10000006i
*
The section **Creating a subclass of NSDocument** within the above article, Creating Document Based Applications


TufTy

----
Or you could override newDocument in an application delegate.
----

Which violates encapsulation completely.  What does the app delegate have to do with individual documents?  I might forgive you if you said NSDocumentController, but the NSApp delegate?

