---
layout: page
title: ClientSpace
---

Any time you're coding, you can split your code into two conceptual groups:


* CurrentSpace (could also be contractor space or code space... whatever), and
* ClientSpace


CurrentSpace is the code of the object you're working on; it's encapsulation, it's the code that other classes will never know about but which is run any time your object receives a message.

ClientSpace, on the other hand, is code that will use your object. For an example, if you're subclassing NSCell, you might have private methods whose ClientSpace is still within that class. But at the same time, you'd have methods like -representedObject which belong entirely to client code--they're there as convenience to the client and shouldn't be taken over by your code.

CurrentSpace is a door, ClientSpace is people who will turn the knob.

For another (non-code) example, files on HFS+ volumes support comments (as seen in the get info window). The comments are only ever in client space--never should they be taken over by your code. You might be interested in displaying them, but changing them out from under the user is bad bad bad. But at the same time, there's lots of code in the file system implementation which clients will never see. So it's never in ClientSpace on the same scale as file comments are.

This can help you view your code as though somebody else is going to be a consumer of it even when it's only ever going to be in one app--which is good design practice. It can also help you sort out the differences between model and controller objects (see ModelViewController for more info on those); model classes tend to have more code relevant to ClientSpace than to CurrentSpace.

Sorry if I've been terribly unclear, I'm tripped up on codeine since the removal of my wisdom (teeth), and half-asleep to boot. -- RobRix

----

This is the only place I've found so far that mentions file comments on HFS+.  I was hoping to write a program that gives easier access to file comments (especially convenient for pictures and such), but this blurb claims that changing those comments would be bad bad bad.  Does that mean that giving access to those comments (not just display, but editing) is just as bad?  If Finder does it why can't I?

And how do you access those comments, anyway?   --ColinTA

Changing file comments out from under the user is bad bad bad. Providing an interface for the user to change them is something else altogether. So sure, let the user mess with them, but never, *ever* use them to store e.g. meta-data. They belong to the user, not to your program. -- RobRix

