---
layout: page
title: AssignAFileToAnApplication
---

Is there a way to assign a file without an extension to an application, so it always opens with the specified app? In the way of Finder's "Get Info" of a file and changing the dedicated application for that file and clicking on "Change for all..."?
I'm creating a file myself and I need it not to have an extension, which is the reason I'm asking all this.

----

This question came up relatively recently, and I believe the answer was no.

----
I believe the system will still use the HFS type/creator of the file if nothing else is available, so you could try setting that on the file and making sure your application is registered to receive them.
----
Ok - Is Launch Services the right place to look for that?

----
No, look in Carbon's File Manager.

----

I wouldn't make any reliance on type codes -- they are supported for compatibility reasons only, as Apple is moving to UniformTypeIdentifier<nowiki/>s.  They are not the primary manner of identifying file types any more, and won't get you any closer to supporting all files.  For example, you will not be able to associate your application with files having no extension and no type code (for example, by running `touch foo` in Terminal).  As far as I know, there's no way to associate an application with "all file types".

----

You could associate an application with all files by editing its Info.plist to accept the public.data UTI, I believe.

----

No, no, I want a file type created by me to be assigned to my application but without extensions... My app shouldn't open for all "public.data UTIs" but for my own file type.

----

Shouldn't be a problem. Make sure your type/creator info is in your Info.plist then set the type/creator info when you save your document. 

The first link on this google code search shows how to set the type/creator info for a file:
<http://www.google.com/codesearch?hl=en&lr=&q=wxFileName%3A%3AMacSetTypeAndCreator&btnG=Search>

Hope this helps.

----
In Xcode, when editing the properties of the target, there's MIME Types and OS Types - which one is the one I should use?

----
OS Types. MIME Types are types in the form "application/pdf" or "text/html", etc. -W

