---
layout: page
title: MasterDetailBindingProblems
---



I have a table view, that is fed by an NSArrayController via bindings. The NSArrayController is fed via the nibs File's Owner which is a basic custom controller (NSObject derived).

Within the custom controller, I have implemented the indexed accessor methods for my internal array. (I need to fire NSNotifications to other parts of the app when things change).

    
- (unsigned)countOfFoo;
- (id)objectInFooAtIndex:(unsigned)index;
- (void)insertObject:(id)object inFooAtIndex:(unsigned)index;
- (void)removeObjectFromFooAtIndex:(unsigned)index;
- (void)replaceObjectInFooAtIndex:(unsigned)index withObject:(id)object;


My underlying object is KVC-clean and all the key methods are properly in place.

All is well, and the NSTableView seems to behave normally. I have never seen the replaceObject... called but here is the current problem.

I introduced a second panel within the same nib, a no frills Inspector panel that is displayed with a direct connection to makeKeyAndOrderFront: from a "Show Info" button (for now) . The panels fields are bound to the respective keys on NSArrayController.selection.

Now, while my app is running. If I add some rows, and then bring up the inspector on one of them, it shows the current values as expected. If I subsequently change the selected row within the NSTableView (without closing the inspector), the inspector is NOT updated accordingly, it shows the initial object's values.

Any thoughts?

Thanks

----

I heard a rumor that it will only setValue when the array itself is changed, not when the value for an object in the array is changed.  After all, all the inspector is looking at pointers, not the data that the pointers point to, correct?  Imagine if you had an NSArray populated with ID's pointing to NSMutableDictionaries.  Good luck figuring out if one sub sub string is changed.  You would either have to use an accessor method or (more likely) post a local notification to update.

Oh, yeah.  Come to think of it, that's why you never saw replaceObject.  It was only changing the underlying value in memory.  Try finding a way to force a replace.


Chris
----
Thanks for the feedback.

Actually, I distanced myself from my larger project where I started this (there is a lot going on in that one) and contrived a focused test project to simulate the problem. Well, now it works as advertised, as the bindings documentation says it should (less the - replaceObjectIn<Key>AtIndex:withObject:). I must have something amiss, bad custom Formatter, Transformer, or something in the other project.

Now I am curious as to how to get the - replaceObjectIn<Key>AtIndex:withObject: utilized though.

Took me a while to realize how to get the indexed accessor methods going (Hint, remove - <Key> and - set<Key>: for the contentArray binding) and I beleive if I remove - objectIn<Key>AtIndex: the - get<Key>:range: will start being called as well.

You're right, the objects are being manipulated directly by the NSArrayController and friends, but the docs do say that as long as everything is KVC, the observers should be notified.

The only other thing I did different in the test project so far is that the File's Owner is a NSWindowController subclass and not an NSObject subclass. Would the File's Owner being an NSResponder have anything to do with it?

