---
layout: page
title: ExposingSubobjects
---

Hello,

Recently I have found myself very lazy with interfaces recently. I was writing a class which had an array as an internal structure, I instinctively started to write accessors like     -(id)controllerAtIndex:(int)n; and     -(void)setController:(id)cont atIndex:(int)indx;. Then I got a brain-fart and just put     -(NSMutableArray*) controllers;. I did the same thing with an NSMutableDictionary * but only exposed the immutable base. I used to get flack for doing the same thing in C++, my rationale then was that C++ coders know how to use     std::vector or     std::map already, so why not let them use them, and spare them the trouble of a new interface. I see the same thing with NSArray, NSDictionary and other cocoa clases. So other than post-processing needs (which sometimes can be solved with     [NSTimer scheduledTimerWithTimeInterval:MY_SMALL_TIME target:self selector:@selector(myPostProcessing:) userInfo:nil repeats:NO];. Combined with     NSNotificationQueue's this can also improve efficiency), is this just laziness, or does it make sense to do this (sometimes)? Where might it be dangerous/safe, where does the appkit use it (    self subviews] count])?
-Jeremy Jurksztowicz

----
Well, as far as I can understand your question, the Cocoa answer is never.     [self subviews] and other methods like it return *immutable copies* of the internal arrays (at least in principle). The reason to avoid this is that someone else could stick a non-controller in your array, or a new controller, without telling your class. In many cases, this could end up catastrophic, especially if you had a separate array that relied on having a certain number of controllers. One way to fix this is to use an [[NSArrayController, and access your own data through it. This would allow you to update your information consistently, and be notified whenever something is changed. Another idea is to provide an *immutable*     -controllers method, and a corresponding *mutable*     -setControllers:(NSArray *)newControllers method. Then you can copy the contents of     newControllers back into your internal array and perform any other necessary initialization. One of the rationales behind using method calls instead of direct access is so that subclasses can add functionality to your base class. Creating reusable code is a great goal. --JediKnil

----
I guess it's a bit different in C++ because of the strict typing. But using array's and dictionaries is so easy because you get such a full interface, and enumerator support. I will stick with exposing the non-mutable types. -JJJ

----

It would also be possible to expose a mutable object, and then write a custom container class that performs the necessary updating. Cocoa uses this concept with, for example,     -[NSMutableAttributedString mutableString], which returns an NSMutableString that automagically synchronizes all changes with its parent object.

