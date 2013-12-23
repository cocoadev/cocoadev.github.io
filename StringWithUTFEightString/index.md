---
layout: page
title: StringWithUTFEightString
---




Can someone explain what the difference is between these two functions?

    
+ (id)stringWithCString:(const char *)cString;
+ (id)stringWithUTF8String:(const char *)bytes;


Is it just that     stringWithCString: must end with a null character?

----

It's mainly that     stringWithCString: is deprecated and should never be used. See General/StringWithCString

----

To be more specific,     stringWithCString: uses an undefined encoding. The encoding it uses may or may not be the one you want, but at least with     stringWithUTF8String: you get predictable results. That's why Apple deprecated     stringWithCString:.

----

FTR, this also applies to -cString and the like. --*boredzo*

*And     General/[NSString defaultCStringEncoding]. Although this will tell you what encoding the *cString* methods will use.*

that probably isn't too useful, though, unless you have a menu item in your application titled 'C String Encoding'. ;) --*boredzo*
