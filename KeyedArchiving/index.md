---
layout: page
title: KeyedArchiving
---

Does anybody know the equivelant pair of the two guys bellow using keyed archiving?

[coder encodeValueOfObjCType:@encode(Class) at:&someClassVar]

[coder decodeValueOfObjCType:@encode(Class) at:& someClassVar]

----

Does that actually work?  I wouldn't have guessed that the Class struct would encode so nicely, but I haven't really delved into the Objective C runtime enough to know.  Anyhoo, I suppose you could use NSStringFromClass() and NSClassFromString() so you can just archive the class name using keyed archiving.   -- Bo

----

Bo, that's interesting -- a good suggestion, and I'll probably follow your advice if I don't find a completely kosher way of doing it.  BTW, The two lines above work like magic. :) -- PB

(My hack as to instantiate the object, encode the object, and on decode, instead of retaining the object, sending it a class message and saving the response in a Class var.)

----

I use NSStringFromClass() for this sort of thing. As for encoding Class, I don't know why it wouldn't work, classes are objects too.

-- RobRix

----

It just seems weird because a) he's using -encodeValue:ofObjCType: not -encodeObject: and b) AFAIK the Class object doesn't support the NSCoding protocol.  So, the coder is somehow both detecting that it's an object rather than a simple struct and figuring out how to properly encode it without the benefit of an -encodeWithCoder: method for the Class object.  -- Bo

PS  But I am interested enough to run a few tests to see what works.  Back in a sec.

PPS  Ok. So, after trying a couple of different styles, it seems that the NSArchiver is able to encode the Class type internally, and encodeObject: seems to use the same encoding method to store the object's class info as well.  Interestingly, NSArchiver was even able to encode and decode properly when I placed the Class variable inside a struct and encoded the struct.  Those Next guys were pretty clever.

----

Thanks Bo.  So are you saying that I can safely encode a Class var using [coder encodeObject:myClassVar forKey:myKey]; ?

----

Actually no.  -encodeObject: (forKey: or otherwise) doesn't work on Class objects; you get an "+[NSObject encodeWithCoder:] selector not recognized" error.  You seem to be right on the money that there's no direct analog to the encodeValueWithObjCType:at: function in the keyed archiving functions.  Converting back and forth to a string seems to be the way to go, although I suppose you could create and NSValue from the Class object and archive that too.

----

I'm still pulling for NSStringFromClass() :) Works great for me, and won't stop working unless they remove the class entirely (whereas an update to it in future could break things using NSValue). -- RobRix

----

I agree that NSStringFromClass() is the way to go.  I tried a quick category on NSCoder implementing -encodeValueOfObjCType:at:forKey: and -decodeValueOfObjCType:at:forKey: by wrapping it up in an NSValue and encoding that, and it worked, but I kept getting a strange error in the decode phase: "malloc(): freed pointer on non-aligned boundary". -- Bo

