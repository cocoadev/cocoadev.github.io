---
layout: page
title: SubMenuControlledByCategory
---

I have several sub menus with dynamic content, similar to the Recent Items controlled by General/NSDocumentController.  Each sub menu is controlled by a category (extension).

I'm looking for suggestions on handling this scenario which would make everyone (except the category) ignorant about a particular sub menu (so that the handling of the sub menu can be isolated into one source file).

The main problem is finding the actual sub menu -- since it is handled by a category, I cannot add an outlet to the instance data (and furthermore, there might be many instances of the object, but the sub menu will be set up during my categories load selector (which is a class selector)).

I have considered three options:

1) using an General/NSDictionary in the Nib and adding the sub menus here (with a symbolic name) -- though then there must be an outlet for this dictionary, and if/when my class (with categories) is put into a framework, it will require the same solution by each program which use it.

2) use the menu items 'tag' field and give each menu item (which hold the sub menu) a unique tag -- but unique (integer) values really suck...

3) give the menu item a unique name and let my category change the name (after locating the item) -- this is probably what I like the best except that a) if the category is missing the menu item will be named wrong and b) if used as a framework in another application, the author of that application will not be able to change the titles (since these will be set up by the category).

--General/AllanOdgaard

----

While I'm too stupid to completely underestand what you're saying, it sounds like number 3 is the best approach.  I'd do a check for the category, and cancel the procedure if it's empty.  Now b, I'm not too sure on what to do... hopefully someone more experienced can shed more light on it... I'm just trying to think this out.

--General/CharlieMiller

----

Are these submenus of your application menubar, or of contextual menus? -- General/KritTer

Currently of the menu bar, but I really would like the ability to easily embed them also in context menus -- though I have yet to find a decent way to do so...

--General/AllanOdgaard

Will you want their content to vary between menu? -- General/KritTer

What do you mean by that? I want a sub menu which is present in both a context menu and the real menu bar to have the same content -- but if two categories both add a sub menu these should naturally not have the same content -- further more the content of a sub menu is not necessarily the same throughout the entire session of my program.

As mentioned, an example of what I want is the systems "Recent Items..." sub menu. You create it in IB, and automatically it gets filled by some one responsible that you do not know -- I'm told that the "Recent Items..." menu item can also go into the dock menu, context menus and similar, and still work w/o problems, although my experience is, that only one of the instnaces will work (but not necessarily the one in the main menu).

Come to think of it, perhaps an General/NSMenuItem extension which provide (overload) submenu: and title: would allow for this (and also delay creation of the sub menu till it's actually needed, plus remove the linear scan through all menus to find the item to modify). But AFAIK there isn't any friendly way to overload a method in an existing class (i.e. non-subclass) and keep a pointer to the old implementation (although possible by manipulation General/ObjC structures).

--General/AllanOdgaard
