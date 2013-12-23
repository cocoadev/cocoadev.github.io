---
layout: page
title: MultipleDefinitionsOfSymbolLinkerError
---



Hello there, I'm having trouble with my framework and I've encountered an error that http://goo.gl/Cx9sQ I haven't before:

    ld: multiple definitions of symbol .objc_category_name_NSBundle_TRAdditions
ld: multiple definitions of symbol .objc_category_name_NSMutableString_TRAdditions
ld: warning prebinding disabled because dependent library: /System/Library/Frameworks/AppKit.framework/Versions/C/AppKit can't be searched 

Does anyone have any pointers on how to solve this problem, thanks. http://goo.gl/OeSCu

----

Yep.  You're compiling     NSBundle (TRAdditions) and     NSMutableStrings (TRAdditions) into both a bundle and your main app (or maybe two bundles), then you're loading the bundle.

That can't really *quite* work, because the method     -[NSBundle myCategoryMethod] needs to refer to exactly one function in one place in memory.  When you load the bundle, there are two places in memory which would like claim to the title.  The behavior is probably undefined, but what will happen is that the runtime will just choose one and forget the other.

The way to solve it, if it's reasonable in your app, is not to include     NSBundle+TRAdditions.m (or whatever you named it) in the bundle.  Only include the header file (to avoid compiler warnings from calling added methods).  At runtime, the methods will be there because they'll be loaded with the main application.

----

I have a similiar situation.  My app runs fine in Debug mode, but under Build I get the following error
    
multiple definitions of symbol _SDImageCellType
multiple definitions of symbol _SDVideoCellType
etc
command usr/bin/gcc-4.0 failed with exit code 1


zeroLink is off.  I have a sneaking suspicion, and a very painful one, that this is because all of my 30 source files import - and only import - the prefix header.  It says "Prefix header for all source files of the 'VL test' target in the 'VL test' project" and everything runs fine in debug!  Can anyone confirm that somewhere it is written that the prefix header is not suitable for build mode?  Because if that's the case, I haven't seen any warning.

----
You probably have those two symbols, whatever they are, defined in the header, rather than declared in the header and defined in the implementation as they should be. But without seeing the code for them, it's impossible to say for sure.

----

If this helps, they are declarations that I wanted to be able to use universally, like how in any file you can say NSWindowDidResizeNotification and there will be no error.  They look like
    
 const int SDImageCellType = 3;

And they are indeed declared in the header file, before the interface declaration even.  I just copied the style I saw in NSCell.h.  Moving them to the implementation makes the app cease compilation much earlier, specifically the first time one of my other classes references the same constant.  How can you create a program-wide constant?

Still, I'm not sure that this is the problem exactly.  Why does it work in debug (ZL) and not in build (no ZL)?  Is it ok to have every header placed in the prefix header and then have every  .m file import ONLY that prefix file?

Oh, and thanks!

----

My copy of NSCell.h doesn't contain "const" anywhere, so I'm not sure what style you're copying.

Anyway, const or not, what you're doing is declaring a global variable. And the correct way to make a global is to declare it in the header:

    
extern const int SDImageCellType;


And then define it in the implementation:

    
const int SDImageCellType = 3;


The first is equivalent to a function prototype, the second to a function body.

----

Wow, I'm sorry; you're right about NSCell.h  Here's what I was trying to duplicate, 
    
typedef enum _NSCellType {
    NSNullCellType			= 0,
    NSTextCellType			= 1,
    NSImageCellType			= 2
} NSCellType;


or actually originally I wanted to extend it but couldn't find out how to extend a typedef and believe me I looked.  So I tried to flat out replace it but that go too hairy.  So I ended up just rolling my own and I guess the const int thing is what I ended up with.  Forgive me but I know next to nothing about C.  It's amazing how much you can do with just the "Obj" part of Obj-C thanks to Cocoa.  I am presently trying out your suggestion...

Ok, that is working, and leaving them before their respective @interface /implementation markers. 

However, this has no effect on the operation of the program.  It worked before having every file import the header with the definitions in it.  But by using the "deployment" build style build fails with the original link error, that the symbol is multiply-defined.  No joy :(.  Can you confirm that the prefix header is not for deployment builds?  Or am I on the wrong track?  This class is the only point where the items are defined, so it can only be that the linker is bringing the file in many times.  I thought that the "import" command was supposed to be smarter than "include" and only bring it in once?

----

A potential solution to multiple symbols in both a framework and application is ld's 'UNEXPORTED_SYMBOLS_FILE'. 

If the framework and application both use the sample piece of code, such as the MoreIsBetter library in my case, you can separate both copies by adding the symbols to an exclusion list in the framework.

-- GrahamMiln

----

