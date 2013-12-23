---
layout: page
title: TheUseOfRetainingObjects
---

Right now, I'm desperately trying to understand what is the use of retaining objects. Since it's something so obviously basic knowledge, there must be some reason to implement it. I want my object to "hold" an instance variable created in a method so this variable can be used in another method of this same object.

For example, I have an object, call it Obj...
Obj has two methods : func1 and func2...
func1 creates an object, say, a General/NSArray called myArray... And retains it...
I need myArray in func2 too...
But when func2 tries to access myArray, I get this error message...

    'myArray' undeclared (first use in this function)

So, I thought : well, I have to declare it in the .h of Obj...
But then, I say to myself : "Why would I get in the trouble of retaining/releasing myArray when I could just declare it in the header and get access to it in any method of Obj ?

I must be missing some very important notion about Objective-C. If anyone can bright light on me, I would be grateful... Thank you...

-- Trax

----

I answered him. --General/DustinVoss

any chance of putting something here for the next time someone comes across this?

----

For future reference, you need to declare the array in the header file.  Do this inside the brackets, just by saying something like this.
    
General/NSArray *myArray;


Now you can access it anywhere from within the m file.  If you need to access it from elsewhere, make a method which returns the array.

--General/CharlieMiller

----

I found General/RetainingAndReleasing helpful.

----

I just had an article published (yes, I expect to be talking about this for quite some time :) ) on the subject of memory management. Hopefully it'll help you figure it out. Here's the URL:

http://www.macdevcenter.com/pub/a/mac/2003/06/10/memory_mgmt.html?page=1

-- General/RobRix
