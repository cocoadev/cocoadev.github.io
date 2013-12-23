---
layout: page
title: ClassFromClassName
---

Hello all; I'm trying to implement a fractal-drawing cocoa app and I want to use a  popup menu to choose which type of fractal to draw. The fractals are different classes, but following the same general protocol. I thought I could name the items in the popup menu after the classes and then simply use:

@classfromstring([sender title]) alloc] init]

is there such a thing?

[[NSClassFromString(@"NSJamesCallender")
----
Inside /usr/include/objc/objc-runtime.h is a function called objc_getClass() which I think is what you're looking for. Note that the function accepts a C string, so you'll either want to wrap the above function call inside another function that accepts an NSString, or change your code to use C strings. If you don't wrap the above fucntion, something to make this more pleasant would be to use #define to make more-friendly function name call objc_getClass().

By the way, there is very likely a "better" way to solve your above problem besides dipping into the Objective-C runtime. I was formulating one to suggest here, but it might be sort of "iffy"...

--KevinPerry

Erm, just above the line, somebody else posted the answer: NSClassFromString(), found within the Foundation functions. -- RobRix
----
Oh whoops, my mistake. Well, that works too. Sorry about that --KevinPerry

---

This works, thanks... but I'll look for a better solution later - right now this seems to be my alternative to implementing a whole plugin-system. thanks! EnglaBenny at macnytt com

You might want to consider using NSCell's -setRepresentedObject: and -representedObject methods when you create or otherwise deal with the menu.  [menuCell setRepresentedObject:[Fractal class]]; --JoeOsborn

