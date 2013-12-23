---
layout: page
title: NibInstantiatingClassFromLibrary
---



The description pretty much says it all.  I've created an Objective-C++ based library (static linkage) which contains a custom NSView subclass (call it MyCustomView).  The public header file for this library contains the definition for MyCustomView, and I'm able to get Interface Builder to parse the header file so that it knows about the class.  I then associated an NSView instance in my interface with MyCustomView (again, no problems).  However, after I compile my application, link it with the external library, and run it, I get the following message:

----

2007-04-25 09:33:45.232 MyApp[6727] Unknown class 'MyCustomView' in nib file, using `NSView' instead.

----

and I can't call any of the methods defined in MyCustomView.

I've searched around for information about this error, and most people encounter it because they forget to compile a class.  However, I've checked the project for my library and MyCustomView is being compiled fine.  My guess is that it's either a matter of timing (the code for the static library hasn't been loaded when the nib file is instantiating it's objects) or that there's some limitation whereby nib files can only load custom classes contained within the application itself.

----
I just did an "nm -f" on the static library and I can see that MyCustomView is defined in it.  However, when I run my application in the debugger and do "po [MyCustomView class]" I get back "MyCustomView is not an ObjC Class".  So there's something weird going on.

I'm going to try instantiating MyCustomView in my code at runtime to see if there's a linking problem...

----
ok, I think I know what's going on now.  If I make a direct reference to MyCustomView in my code (eg. by instantiating it), then the "Unknown class" messages go away and everything works fine.

So what appears to be happening is that, when linking the static library, the linker doesn't find any references to MyCustomView in the code (since the only reference is in the nib file), and it doesn't bother linking it into the application.  Either that or there's some sort of "lazy" loading happening whereby MyCustomView isn't loaded since it isn't referenced in the code.  I'll have to look into this a bit more...

----
I've run into this before. Try adding the     -ObjC flag to your build settings (something like "Other C Flags") to avoid this. --JediKnil

----
I just tried that in the library, the application, and both and it didn't fix the problem.  I'll try     -ObjC++ as well just to see...

Nope that didn't help either.  I'm going to look into the linking options and see if there's any settings there which could be related since I think this is a linker issue.

----
wow, that was a tough one.  I finally fixed the problem by checking the "Perform Single-Object Prelink" setting in the static library build options.  The description of that option is:

----

Activating this setting will cause the object files built by a target to be prelinked using 'ld -r' into a single object file, and that object file will then be linked into the final product.  This is useful to force the linker to resolve symbols and link the object files into a single module before building a static library.  Also, a separate set of link flags can be applied to the prelink allowing additional control over (for instance) exported symbols. [GENERATE_MASTER_OBJECT_FILE, -r]

----

I'm not sure exactly how that fixes the problem, but I'm guessing that it forces all symbols to be linked into the executable even if they aren't directly referenced by the code.

