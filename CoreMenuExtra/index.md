---
layout: page
title: CoreMenuExtra
---

Here are some useful private APIs for manipulating the Menu Extras currently in the menu bar:
    
typedef struct _OpaqueMenuExtra OpaqueMenuExtra;
OSStatus CoreMenuExtraGetMenuExtra(CFStringRef identifier, OpaqueMenuExtra **menuExtraOut);
OSStatus CoreMenuExtraAddMenuExtra(CFURLRef path, SInt32 position, void *_0, void *_1, void *_2, void *_3);
OSStatus CoreMenuExtraRemoveMenuExtra(OpaqueMenuExtra *menuExtraIn, void *_0);


----

I'm curious to know where these declarations come from. *--boredzo*

----

It would appear to be from these mailing list posts:
http://www.omnigroup.com/mailman/archive/macosx-dev/2001-October/032802.html
http://www.omnigroup.com/mailman/archive/macosx-dev/2001-October/032806.html

� ElliottCable: http://elliottcable.name/

----

**Update:** I had to modify the signatures to shut up some compiler warnings; I updated the above signatures to the ones that worked for me.

**Note carefully!**     CoreMenuExtraGetMenuExtra() **does** take a metapointer to an     _OpaqueMenuExtra, whereas     CoreMenuExtraRemoveMenuExtra() takes a simple pointer: you must indirect one extra time for     CoreMenuExtraGetMenuExtra(), as in the following example code (extracted from MacUIM: http://www.google.com/codesearch/p?hl=en#u6pAglFAHSU/trunk/Sources/MacUIMPrefPane.m&l=597 )

    
void *extra; if ((CoreMenuExtraGetMenuExtra((CFStringRef)extraID, &extra) == 0) && extra)
                  CoreMenuExtraRemoveMenuExtra(extra, 0);

