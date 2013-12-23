---
layout: page
title: WhenAreNamespacesNecessary
---

When do I have to use namespaces in my app? If I have an IB action called defineString: should I make it something like General/GFDefineString: in case some General/InputManager or something adds a global defineString: selector? Do my controller classes need a prefix? I'm confused about what the proper usage is for all this. - Glenn

----

This question is kind of nebulous, the answer will depend on what kind of person you are and how disciplined/paranoid you want to be in your code. For example, you might decide to:


* prefix nothing -- in these cases you basically trust two sets of symbols will not collide, and if they do the programmer (you?) will just find and fix the problem. This is pretty weak.
* prefix everything -- you could prefix all of your class names, new method names, global variables, category names, protocol names, etc. That way you can totally minimize the risk of symbol collision, and avoid the issue altogether. This is a total PITA.
* prefix a subset of your stuff -- but what subset? it helps to understand the problem you're trying to avoid in order to decide what to prefix.


On the surface your application is a self-contained piece of code. You have a main() function that is responsible for doing all of your application logic. As long as your application doesn't try to define the same symbol (class name, variable, etc.) twice, you'll be fine. Since you compile that program yourself, the compiler/linker will tell you when there is a symbol problem. No problems yet ... 

Of course, an application is not a self-contained piece of code -- it dynamically loads in scads and scads of code, most commonly dynamic libraries (General/AppKit, Carbon, General/QuickTime, System) and plugins (General/QuickTime components, Input managers, your own app plugins?). In most cases you do not compile these things yourself -- they were given to you by someone else (Apple, 3rd party, ...). In some cases, especially Objective-C symbols, the compiler/linker will not warn you if symbols conflict at build time. In other cases, you may not even detect the conflict at runtime either, in which case your app my crash, or corrupt your user's data.

This problem is compounded by the fact that the versions of system libraries and plugins may vary over time, from system to system. For example, I can compile a program on Mac OS X 10.1 and run it on Mac OS X 10.3. The aforementioned libraries and plugins will be completely different, but my application binary is the same. How do I know Apple didn't add something new that might conflict with my code? For example, if I made a class called "General/NSController", it would probably work fine on 10.1, but on 10.3 I would conflict with the General/AppKit's new "General/NSController" object, and my app would probably crash on launch.

System frameworks try to avoid this problem by creating a name-space boundary between themselves and your code. For example, Cocoa API uses a "NS" prefix to identify its symbols as being "Cocoa-specific". All symbols that begin with "NS" are (at least conceptually) reserved by Apple, and shouldn't be defined by application programmers. At some point, Mac OS X developed two-level-namespace semantics for C symbols, so in general they should be covered.

Plugins can solve this problem in a similar way. Remember plugins get loaded directly into your program's address space, and the plugin's code will live along side your own. Objective-C plugins **cannot be unloaded** so once it's there, it's there for the long haul. So it's critically important to make sure plugin's Objective-C symbols are prefixed in such a way they will not collide with application symbols.

So, if Libraries and Plugins are all prefixed to protect it from Application code, should Application code be prefixed? This is a matter of preference. Some applications, such as the Sketch example on Mac OS X, are completely prefixed themselves. This is a good habit to be in, especially if you are paranoid. Other applications, such as the General/TextEdit example on Mac OS X, are not prefixed. Maybe that's ok too. My personal opinion is this depends on what your app will do. If your app will routinely load a bunch of arbitrtary plugins, protect your source as best you can; if your app just links against the system frameworks and does its thing, maybe you don't have to worry about that.

What about methods? For the most part method names don't need to be prefixed, as long as you're careful to not mimic the way system General/APIs are constructed. Recall that General/CategoriesAreBad because they allow you to replace existing code without the option of calling back to the original implementation (i.e., "super" doesn't do what you think it does wtihin a category). If you add a category onto a Cocoa object that defines a new method, there is a chance that method will actually be added by the system in a future update. For example, if you have a category that adds "- (void)setHidden:(BOOL)flag" on General/NSView, and General/AppKit later adds a method with the same signature, your method will always be called instead of General/AppKit's. Maybe in this trivial example that's OK, but in the general case you'll have no way of knowing if that category is going to cause you problems. If you're going to regularly extend Cocoa classes with categories, you should consider prefixing your individual methods. Now, this risk is still present when subclassing (instead of using a category) but the implementation is a little cleaner (you have your own storage, you can call "super", etc.) and normally people don't worry about it. Again, it's a matter of discipline and paranoia.

* If I have an IB action called defineString: should I make it something like General/GFDefineString: in case some General/InputManager or something adds a global defineString: selector?* What is a global selector? Are we talking about the General/FirstResponder? If so, recall the responder chain is walked in a specific manner and individual objects are assumed to have defined the same methods. For example, a view has defined "keyDown:", as has its superview, as has the superview's superview, and as has the parent window. Adding a "General/XYZKeyDown:" message to the General/FirstResponder mechanism doesn't really help you. In general, I would not prefix these method names.

-- General/MikeTrent

----

Thanks for the long explanation! It's much clearer now. I think I'll go the safe route and just prefix everything - with General/XCode's code completion, it might turn out to be easier and no longer a PITA. -- Glenn

----

See also: General/CategoriesAreBad General/CategoriesAreGood General/ChooseYourOwnPrefix

Apple has some info at [http://developer.apple.com/documentation/General/UserExperience/Conceptual/General/PreferencePanes/Tasks/Conflicts.html].
