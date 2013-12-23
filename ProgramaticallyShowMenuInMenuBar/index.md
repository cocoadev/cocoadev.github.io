---
layout: page
title: ProgramaticallyShowMenuInMenuBar
---

This article describes how to programatically show a menu in the menu bar. This can be useful when you want to implement a menu like the Help menu in Mac OS 10.5 which shows when you press Command-Shift-/.

As explained in the article below, there's no public API to accomplish this. Instead private APIs must be used.
 --SaileshAgrawal

----
**Step 1: Try public APIs**
Cocoa only provides APIs to show contextual menus. The Carbon API MenuSelect(Point startPt) is interesting but it's difficult to use because you need to know the location of the menu you want to reveal.

The site www.cocoabuilder.com isn't helpful either. There are a couple of people asking the same question but with no answers.

----
**Step 2: Reverse Engineer the Help Menu**

The next step is to figure out how Apple shows the help menu when you press Command-Shift-/.

The Help menu in Mac OS 10.5 is implemented in the framework /System/Library/PrivateFrameworks/Shortcut.framework. Running class-dump on the framework shows that most of the work is done by the class SCTSearchManager. For example, calling [[SCTSearchManager sharedSearchManager] toggleSearchMenu:nil] causes the help menu to show.

Stepping into toggleSearchMenu: reveals that it calls a private HIToolbox API PostFakeMenuSelectionEvent(). It looks like the API has the following signature:
   void PostFakeMenuSelectionEvent(MenuRef menu, void *unkown);

----
**Step 3: Solution**

Now that we have a way of showing a menu here's how we can use it.

    
   extern void PostFakeMenuSelectionEvent(MenuRef menu, void *unkown);
   extern MenuRef _NSGetCarbonMenu(NSMenu *menu);

   - (IBAction)onMenuAction: (id)sender
   {
      MenuRef menuRef = _NSGetCarbonMenu(myMenu);
      PostFakeMenuSelectionEvent(menuRef, NULL);
   }


----
**Note About Private APIs**

One risk of using private APIs is that if Apple removes the API then your application may not launch or crash. You can avoid this by looking up the private API dynamically. If the lookup fails then do something else.

Instead of declaring the function extern use the following API:
    
typedef void (*PostFakeMenuSelectionEvent_Private)(MenuRef, void *);
PostFakeMenuSelectionEvent_Private sPostFakeMenuSelectionEvent = NULL;

NSBundle *bundle = [NSBundle bundleWithIdentifier:@"com.apple.HIToolbox"];
NSString *path = bundle bundlePath] stringByAppendingPathComponent:@"[[HIToolbox"];
void * hiToolboxLibrary = dlopen([path UTF8String], RTLD_GLOBAL | RTLD_LAZY);
sPostFakeMenuSelectionEvent = (PostFakeMenuSelectionEvent_Private)dlsym(hiToolboxLibrary, "PostFakeMenuSelectionEvent");
dlclose(hiToolboxLibrary);

if (sPostFakeMenuSelectionEvent == NULL) {
   // The private API no longer exists, do something else
} else {
   sPostFakeMenuSelectionEvent(myMenu, NULL)
}

