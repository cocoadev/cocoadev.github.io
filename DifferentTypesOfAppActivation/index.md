---
layout: page
title: DifferentTypesOfAppActivation
---

On Mac OS X there are two ways your application can be activated:


* 1. applicaiton is activated and all its visible windows come to the front. if it has no windows then the first minimized window is un-minimized.

1.a. Command-Tab

1.b. Click on dock icon

1.c. Double click app in Finder

* 2. application is activated and the key window is made front most. All other windows remain the same.

2.a Click on a window in your App when it's inactive

2.b. Hide or quit the currently active app making your App the new active app.



In both situations the Application delegate will receive a applicaitonWillBecomeActive: and applicationDidBecomeActive: event.

Knowing which type of activation occurred is useful when your Application has custom windows that need to behave differently. Unfortunately there's no explicit way to tell the two different activations apart. This article discusses how to differentiate between the two types of activation. --SaileshAgrawal

----
**Step 1: Partial Solution**

It's easy to know when events 1.b (Clicking on a dock icon) and 1.c (Double click on the App icon in Finder) occur. They both result in the following event:

    
- (BOOL)applicationShouldHandleReopen:(NSApplication *)theApplication hasVisibleWindows:(BOOL)flag


Unfortunately we're still left with 1.a (Command-Tab to the application).

----
**Step 2: Another partial solution**

It's also fairly easy to know when 2.a (Clicking on a window in your App) occurs. Simple subclass NSWindow and override:

    
- (void)mouseDown: (NSEvent *)event
{
   if ([NSApp isActive] == NO) {
       // This is event type 2.a.
   }
}


Unfortunately this still leaves 2.b.

----
**Step 3: Solution**

One implicit difference between the two types of events is the ordering of windows. If your Applicaiton has more than 1 window opened a simple thing to do is to check if all your windows have become front most when it became active. Here's the code:

    

- (void)applicationDidBecomeActive: (NSNotification *)note
{
   if (AllWindowsAreFrontMost()) {
      // This is activation type 1
   } else {
      // This is activation type 2
   }
}

BOOL AllWindowsAreFrontMost()
{
   if (![NSApp isActive]) {
      return NO;
   }
   NSArray *appWindowsArray = [NSApp windows];
   int appWindowCount = 0;
   int appWindowsappWindowsArray count;

   // NSApp returns windows back to front so we have to flip the order.
   NSEnumerator *e = [appWindowsArray reverseObjectEnumerator];
   NSWindow *window;
   int i = 0;
   while ((window = [e nextObject])) {
      if ([window isVisible]) {
         appWindows[i] = [window windowNumber];
         i++;
         appWindowCount++;
      }
   }
   if (appWindowCount < 2) {
      return YES;
   }
   int globalWindowsCount;
   NSCountWindows(&globalWindowsCount);
   int globalWindows[globalWindowsCount];
   NSWindowList(globalWindowsCount, globalWindows);

   if (appWindowCount >= globalWindowsCount) {
      return YES;
   }

   /*
    * Go through all the global windows. The app window may not be first one
    * if there's a top most window on screen. Instead, just find the first
    * app window and then make sure the rest are in sequence.
    */
   int j;
   for (i = 0, j = 0; i < globalWindowsCount && j < appWindowCount; i++) {
      if (globalWindows[i] == appWindows[j]) {
         j++;
      } else {
         if (j != 0) {
            return NO;
         }
      }
   }

   return j == appWindowCount;
}


