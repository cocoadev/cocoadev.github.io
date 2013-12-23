---
layout: page
title: CalcMenuSizeAndSetMenuHeight
---

I've got a menu, but I need to re-size its height in some cases. This is the thing: I have to query the menu's size. For that, I need to call CalcMenuSize. If it's larger than a certain height, then I want to use SetMenuHeight to set the menus height. However, after calling CalcMenuSize, it doesn't work - it doesn't use the size I set. If I don't call CalcMenuSize it works, but then I can not query it for its size before I set the new height... What am I doing wrong?
    
MenuRef myRef = [aMen menuReference]; //this is a category using NSGetCarbonMenu
CalcMenuSize(myRef);
float height = GetMenuHeight(myRef); //returns 0 if I don't call CalcMenuSize first
if (height > 500)
{
//doesn't work!!, only if I don't use CalcMenuSize, but then I can't query it for its size - vicious circle?
SetMenuHeight(myRef, 500);
//GetMenuHeight(myRef) returns 500 here. So it has been set, but it doesn't work... I somehow need to UPDATE the menu?
}


Currently, I'm     copying the menu, do calcmenusize on it, ask for its height and set the height to the original menu, not the copied one. ugly, but works... Any better solutions?

----

You might be running into an optimisation in the Carbon Menu Manager. Bear in mind this API is possibly the worst in the original Mac toolbox and the Carbon variant still contains way too much legacy that goes back to the Lisa. Not sure why they clung to this code so desperately but there it is. Carbon menus are drawn by an MDEF and the MDEF is responsible for calculating the menu size, etc. Given that to calculate the height it has to iterate over all the items and add up the individual heights, and that this code is very old and therefore expected to run on slow 68K hardware(!) the menu height thus calculated is no doubt cached to avoid iterating again. A flag to say "I calculated this" must be set and it's probably also set by SetMenuSize. So unless you can invalidate that flag subsequent calls to SetMenuSize are being ignored. You might try inserting a dummy item into the menu then removing it again - that might clear the flag allowing the full calculation or a deliberate set to be done again. I'm totally guessing here, but old code used to do lots of these kinds of optimisations. --GC

