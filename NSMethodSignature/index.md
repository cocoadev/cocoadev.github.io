---
layout: page
title: NSMethodSignature
---



NSMethodSignature is the type signature of a method on a class or an object. You typically retrieve them by using NSObject's -methodSignatureForSelector: and +methodSignatureForSelector: for instances and classes, respectively.

Commonly, method signatures are used to create instances of NSInvocation, and if you're writing an NSProxy subclass you'll probably want them too. They're very froody.

There is an undocumented private method, +signatureWithObjCTypes:, on NSMethodSignature which (apparently) allows you to create an arbitrary NSMethodSignature on the fly; this would be a great boon for many runtime hacks (see HigherOrderMessaging for greater detail into the subject).

Note that this method is exposed as of Leopard, so it probably doesn't belong in "Undocumented Goodness" anymore.

Documentation for it can be found by looking up the GNUStep or OpenStep specifications. An example argument to signatureWithObjCTypes: is "@^v^ci", a method which takes an integer and returns an object. The first bit, @, is the return type of id; ^v^c is arguments of a void* and a char*, the hidden arguments of every ObjC method, and the final i is the 'real' argument.

----

Um... I hate to disagree with you, but the "hidden" arguments are self and _cmd - an object and a selector, respectively. Their types are '@' and ':'. So your example of a signature for a method that takes an int and returns an object should be '@@:i'.

You are correct, sort of - NSMethodSignature deals with functions, not just with methods. After navigating the typedef maze, an id, in pure C, is a void*, and a SEL is a char*. I was thinking in those terms when I wrote that description. It does work, but your way should work too, and is probably better since it's higher level.

For the sake of clarity, you really want to use the highest-level type available. I'd recommend using "@@:i" in this case. Also, you probably don't want to think of an id as a (void *), even at this low level -- there's nothing in the language spec that guarantees that it is (void *), so this is an implementation detail that Apple could change in the future.

Actually, according to Apple documentation, the encoding strings *themselves* are an implementation detail that Apple could change in the future. For maximum safety (and for the highest-level type information available), I'd probably recommend using something like this instead:

[[NSString stringWithFormat:@"%s%s%s%s", @encode(id), @encode(id), @encode(SEL), @encode(int)] UTF8String]

Bah! *Everything* is a void * when you get right down to it ;)

Yeah it's not exactly an implementation detail...id is typedef'd as a pointer to the objc_object struct, which is in itself essentially a pointer to void. CFTypeRef is a pointer to void. Any pointer is really a pointer to void. The sizeof(id) == sizeof(NSString *) == sizeof(NSRange *) == sizeof(int *) == sizeof(long long *) == sizeof(void *) == etc. Currently, sizeof(void*) == 4 but there was some discussion regarding whether it would change to 8 with 64-bit systems (it didn't). But, in the end, all pointers point to void.

*I'd expect that if you compiled for the ppc970 (G5) only, you'd have eight-byte pointers, because otherwise you'd be unable to address all of the system's memory. Does anybody know if Apple's provided much (or any) support for building fat packages a la OpenStep that have different binaries for 32- and 64-bit systems?*

I couldn't find the HTML page that I saw this on, but check out page 4 of http://www.apple.com/macosx/pdf/Panther_Unix_TB_10082003.pdf. I quote:     sizeof(void *) == 4, sizeof(void (*)(void)) == 4. The only 8-byte sizes for the G5 are     long long and     long double, although this can be disabled for 32-bit compatibility. There was a discussion about this on macosx-dev a while ago. I think Apple's released some documentation about building FAT packages of some sort. Or maybe I was just imagining it.

*Maybe I'm alone in thinking this, but that just doesn't make sense (the size of pointers, I mean). Most of the point of having a 64-bit system is being able to address more than you can with a four-byte pointer... Maybe bytes have changed size :P*

----
Note:

sizeof(double), sizeof(long double), and sizeof(long long) is 8 bytes for the G4 as well. In the case of sizeof(long double); the compiler emits a warning that it may change in the future.

*Should we move this to separate page? It's a relevant discussion but could be given a better title than NSMethodSignature.*

