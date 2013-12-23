---
layout: page
title: NSNull
---

NSNull is the FoundationKit's equivalent of nil and NULL. It's used as a placeholder in collections which do not allow you to set an item to nil. See also: FoundationCollections

----

Nothing is a concept that goes by many names:


* nil
* Nil
* NULL
* NSNull
* 0
* NO
* false
* zero
* nada
* zip
* zilch


As you can see, it's a master of disguise. Handle with care.

Don't forget the most ancient of all...


* /dev/null


--Anonymous Guy

If you're talking types, you can also add void.

-- RobRix

... And none -- which coincidentally seems to be the point of this page...

and [           ] (imagine nothing in the preceding box.)

----

The difference of course is that they're not all semantically equivalent.  NULL is defined to be a 0x00 byte, while nil is any invalid object pointer which might or might not equall NULL depending on the implementation.  This rooted in the fact that it is convention to denote False with zero and True with one, a completely arbitrary choice.  It's somewhat misleading because False does no inherently mean Nothing, but they've become somewhat linked due to notational convention. --OwenAnderson

----

It's not clear exactly what is meant here by "nil is any invalid object pointer" -- <code>nil</code> is certainly not the same as a pointer to, for example, a freed object.  It is certainly the case, however, that <code>nil</code> is **not** the same as <code>false</code> or <code>NO</code>.

<code>[NSNull null]</code> is a singleton value used to represent a <code>nil</code> value in a situation which prohibits the use of <code>nil</code> -- for example in a collection class.

----

And NULL is not "defined to be a 0x00 byte." It's defined to be an alias for the null pointer, which while equivalent to 0 on most architectures, is not portably so. Not to mention that it's pointer-width, not byte-width.

----

As for the <code>nil</code> statements made above, I am pretty sure <code>nil</code> in fact is zero (with pointer size). At least the ObjC docs say it is defined as <code>(id) 0</code> and somewhere else it is stated that is equivalent to <code>NULL</code>. If this were not the case, something like <code>if (object)</code> would not work.

--MarCocoa

----
    nil just **happens to be** zero, but it's not guaranteed as such. The C standard guarantees that     NULL will be zero when you convert it to an integer, and that it will compare as equal to zero, but it does not guarantee that the actual     NULL pointer itself contains zero.     nil is just     NULL by another name, so it'll follow those rules as well. That said, you won't find any modern architecture on which     NULL is not implemented as zero.

----
As we probably all agree, it is utterly unimportant and purely theoretical, but as said, the Objective-C Language Reference by Apple states that <code>nil</code> is defined as <code>(id) 0</code>  and not as <code>(id) NULL</code> ...

----
And converting the integer zero to a pointer type gets you, guess what,     NULL. The language standard guarantees that     (id)0 is equivalent to     NULL except for the type.

