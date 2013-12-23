---
layout: page
title: UnknownClassError
---



I've built an app pretty much to the spec outlined in O'Reilly's "Building Cocoa Applications", made myself a nice little controller, instantiated, built it, and every time I try to test the interface or run the program I get the following error in my console:

2003-01-29 18:31:13.859 Interface Builder[932] Unknown class `General/LeetController' in nib file, using `General/NSObject' instead.

IMPOSSIBLE!  It's right THERE!  IB seems to know about it quite well, since it's sitting in the instances panel and such.  What is going on?
----
Just wondering, is your General/LeetController class a subclass of General/NSObject? IB might not know how to use custom root classes. --General/KevinPerry

----
I think that the error you are getting may be because you have not created the files for the class (ie ctrl-click on the non-instantiated object and go down to create files for class).

-- (noob on the scene)
             Xypher

----
Yet another cause may be the class file (.m) not compiling.

Check the General/XCode project. Confirm that the checkbox for the class file for correct target is checked.  This  problem does not show up as an error in your compile and link, but manifests as the General/UnknownClassError at runtime when the Nib is attempting to instantiate the missing class during nib wake-up.  --  General/PresterJohn
