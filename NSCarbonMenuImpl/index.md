---
layout: page
title: NSCarbonMenuImpl
---

NSCarbonMenuImpl is a class accessable from the -_menuImpl method of NSMenu. It can be used mainly for creating a pop-up menu anywhere on the screen using popUpMenu:atLocation:width:forView:withSelectedItem:withFont:

Undocumented, and liable to break at any time too ;)

Some other methods are:
    
+ (void)initialize;
+ (void)setupForNoMenuBar;
- (void)dealloc;
- (void)setMenu:fp8;
- menu;
- (void)itemChanged:fp8;
- (void)itemAdded:fp8;
- (void)itemRemoved:fp8;
- (void)performActionWithHighlightingForItemAtIndex:(int)fp8;
- (void)performMenuAction:(SEL)fp8 withTarget:fp12;
- (void)setupCarbonMenuBar;
- (void)setAsMainCarbonMenuBar;
- (void)clearAsMainCarbonMenuBar;
- (void)popUpMenu:fp8 atLocation:(NSPoint)fp12 width:(float)fp20 forView:fp24 withSelectedItem:(int)fp28 withFont:fp32;
- (void)_popUpContextMenu:fp8 withEvent:fp12 forView:fp16 withFont:fp20;
- (void)_popUpContextMenu:fp8 withEvent:fp12 forView:fp16;
- window;


----
Does anybody know how to make a popped up menu go away programmatically after popping it up? The only way I can get a menu to go away is by either hitting escape or clicking, any ideas?

