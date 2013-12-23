---
layout: page
title: IsPointerValid
---

Is there anyway to check whether the object to which a pointer is pointing to has already been deallocated without generating a sigbus error (obviously) and without doing anything like enabling NSZombies?

**
No. This is not possible.
**

----

Well, if it was, then it would mean that memory could never be reused. So e.g. making a loop of 1.000.000 iterations, where you'd allocate and then de-allocate an object, would consume the memory of 1.000.000 objects, since each object pointer had to be unique.

----

No. If it was possible, you could still reuse memory simply by deallocating it. Of course, if you're already making mistakes in your program (losing track of when memory is created or destroyed) there's no guarantee that a given pointer still points to the thing you think it does. And that's what memory management is really all about. Consider this is exactly the same as entries in an NSDictionary. You can ask the dictionary if an object is on file for a specific key, but you won't know what that object is until you read it. @"ApplicationName" might point to @"Stickies" just as easily as @"The document '%@' could not be loaded because an error occurred: '%@' Press any key to reboot your computer, sucker!\n"; it's up to you to keep it all straight.

As for 1,000,000 objects, that problem is even true today in the scope of the application's autorelease pool. Go into a loop that creates 1,000,000 autoreleased objects. By the time you exit the loop, your app has 1,000,000 autoreleased objects in its autorelease pool, and your app may suffer from memory related maladies (UI becoming unresponsive due to deallocation, swapping, etc.). Tools like ObjectAlloc will show an abnormal spike in the number of instances of that object, helping you track down these performance problems. -- MikeTrent

----

As I understood the first post, he wants to do something like:

id myObj = [[SomeObj alloc] init]

... lots of code ...

BOOL isValid = [myObj areYouStillValid];

If you interpret it as the 'isValid' should return YES just when myObj is a valid object, rather than the originally allocated instance of SomeObj, then yes, it could re-use memory. But this was not how I understood it.

And I know I didn't explicitly say 'release' above, but 'de-allocate' for me means release, not 'add it to the systems auto release pool for later disposal' -- please give me some credit!

