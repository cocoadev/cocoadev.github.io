---
layout: page
title: UserIcon
---

How do you find the General/NSImage for a specific user? (i.e. like that shown on the login window)

----

How about this?  
    
General/NSData* imgData = General/[[[ABAddressBook sharedAddressBook] me] imageData];
General/NSImage *userImage = General/[[NSImage alloc] initWithData:imageData];


- General/JustinWilliams

----

No, I would like the images for all/any-of the users.

Currently my address book shows two people - me and apple. The other two registered users on my machine don't show up.

----

If you have the users uid (in this case 503) then you could just load the image from:

/Library/Caches/com.apple.user503pictureCache.tiff

----

Is there a way to get a user's associated image that doesn't involve the address book framework? 

----

Yep you could use General/DirectoryServices, specifically dsAttrTypeStandard:Picture
I believe theres some sample code online demonstrating this, although I couldn't find it with a quick google search.

----

It seems like DS may be a good route. One question to the original poster: (out of curiosity) why can't you use the AB framework? It is for a daemon or a web thingie?

----

No, I'm just writing a framework, and for simplicity's sake I would like to not rely on the AB framework, especially if it's for one line of code that gets the user's image.

----

With all due respect, that's a kind of funny reason, considering you only have to link against the framework, and all you need to do is something like     General/NSData *imageData = General/[[[ABAddressBook sharedAddressBook] me] imageData]; General/NSImage *userImage = General/[[NSImage alloc] initWithData:imageData];. If you used any other method, you would have much more code (it would certainly be much less simple). Don't be afraid to link!

----

He or she wants to grab the user icon for any user on the machine; not just the user currently logged in, or those who happen to be in the address book. Also, keep in mind that if you do use the address book, the *me* method may return nil in some special cases. So, although it seems like a good solution for some projects, it's not what the original poster needs in this case.

----

Yes, and I still have no good solution, the best I came up with was by using the users uid, e.g. for uid=503 then
"/Library/Caches/com.apple.user503pictureCache.userImage" - this file is actually a tiff.
(and older versions of OSX used to name it .tiff rather than .userImage)
 - Not sure if this would work if I wasn't admin...  General/RbrtPntn

----

This is very bad to rely on. As the name implies, anything in     Caches is, well, a cache, and can disappear at any moment. The contents of     Caches exist only to make things faster, and anything stored there should be able to be recreated if deleted. The General/DirectoryServices approach is the correct one here.
