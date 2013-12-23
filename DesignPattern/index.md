---
layout: page
title: DesignPattern
---

A Design Pattern is a reocurring solution to a common software engineering problem. The idea behind design patterns is to not continually reinvent the wheel.

Design Patterns differ from Templates in that they do not contain executable code. http://goo.gl/OeSCu

An example of a Design Pattern is the Listener (Observer). A Listener is an object which waits for information from another object. Now, it is clear that this is a very useful basic idea. Listeners are used in all aspects of Mac OS X and Cocoa for many purposes, not the least of which is GUI management. However, rather than stumble over the same design issues whenever you need to implement a type of listener, the Listener is a documented design pattern that features prepared solutions to these issues. You don't need to discover for yourself that all the Observers for an object can be stored in a single array, because this is included in the Design Pattern.

For more on design patterns, including many examples:
http://www.dofactory.com/patterns/Patterns.aspx
http://www.vincehuston.org/dp/ (old url http://home.earthlink.net/~huston2/dp/patterns.html)


------------

Question: Is there any place on the net to discuss advanced programming techniques, problems arising when trying to apply patterns and similar (preferably related to CeePlusPlus), just like this WikiWikiWeb is?

How about the Portland Pattern Repository? http://www.c2.com . It's the first WikiWikiWeb.

----
**Digest/Directory of standard Gang of Four design patterns in Cocoa** 
 
It is informative to note that many of the famous design patterns in "Design Patterns" were first recognized in NeXTstep, a precursor to Cocoa, and in fact "Design Patterns" provides many such citations.  Many of the patterns exist within Cocoa with different names than the Gang of Four gave them for the "Design Patterns" book.  

The following is a digest/directory of standard Gang of Four design patterns and their names, roles, and relationships within Cocoa.  For each pattern, there are links to pages within this site including discussions, named Cocoa classes that participate in the patterns, and commentary about the patterns in the context of Cocoa.

The place to start is Apple�s own design patterns documentation https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaDesignPatterns/CocoaDesignPatterns.html


**Creational Patterns** 
  
***Abstract Factory**   
Creates an instance of several families of classes *

NSObject  Objective-C class methods are sometimes call factory methods.  This pattern is intrinsic to the Objective-C language.   Every Objective C class is potentially an abstract factory.  This pattern is also used to implement Cocoa ClassClusters.  ShouldClassClusterPlaceholderBeASubClass http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDesignPatterns/index.html

***Builder**   Separates object construction from its representation * 
  
NSDocumentManager? � Builds NSDocument instances with NSWindowManager(s), handles recent documents menu, builds document related responder chain�
NSApplication? � Builds main menu, initial responder chain, run loop�

***Factory Method**   Creates an instance of several derived classes * 

Objective-C class methods are sometimes call factory methods.  This pattern is intrinsic to the Objective-C language. 
NSObject   ClassClusters.  ShouldClassClusterPlaceholderBeASubClass   http://developer.apple.com/documentation/Cocoa/Conceptual/MemoryMgmt/index.html

***Prototype**   A fully initialized instance to be copied or cloned * 
 
NSCell is a Prototype when used with NSMatrix.

***Singleton**   A class of which only a single instance can exist * 
 
SingletonDesignPattern SingletonAlternatives NSApplication NSNull NSFileManager NSWorkspace
http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDesignPatterns/index.html http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaObjects/Articles/CreateSingleton.html http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaObjects/Articles/ObjectCreation.html 



**Structural Patterns **

***Adapter**   Match interfaces of different classes *

http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDesignPatterns/index.html
 
***Bridge**   Separates an object�s interface from its implementation *
 
***Composite**   A tree structure of simple and composite objects *

http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDesignPatterns/index.html WrappingIsBad
 
***Decorator**   Add responsibilities to objects dynamically *

http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDesignPatterns/index.html
 
***Facade**   A single class that represents an entire subsystem * 

NSImage - NSText and NSTextView encapsulate the functionality of complex text layout and editing which is internally implement with NSTextStorage, NSLayoutManager, and other classes.

http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDesignPatterns/index.html

***Flyweight**   A fine-grained instance used for efficient sharing * 
NSCell 

***Proxy**   An object representing another object * 

http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDesignPatterns/index.html



**Behavioral Patterns ** 

***Chain of Resp.**   A way of passing a request between a chain of objects * 

http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDesignPatterns/index.html

***Command**  Encapsulate a command request as an object * 

http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDesignPatterns/index.html

***Interpreter**   A way to include language elements in a program * 

Value Transformers?

***Iterator**   Sequentially access the elements of a collection * 

NSEnumerator  http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDesignPatterns/index.html

***Mediator**   Defines simplified communication between classes * 

NSObjectController, NSArrayController, NSTreeController, and NSUserDefaultsController
http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDesignPatterns/index.html

***Memento**   Capture and restore an object's internal state * 

NSCoder NSKeyedArchiver and NSKeyUnarchiver 
http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDesignPatterns/index.html

***Observer**   A way of notifying change to a number of classes * 

NSNotificationCenter 
http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDesignPatterns/index.html

***State**   Alter an object's behavior when its state changes * 

***Strategy**   Encapsulates an algorithm inside a class * 

NSLayoutMananger NSFormatter?

***TemplateMethod**   Defer the exact steps of an algorithm to a subclass * 

http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDesignPatterns/index.html

***Visitor**   Defines a new operation to a class without change * 



**Cocoa Unique Patterns ** 
  
***Target/Action**   A variation/simplification of the Command pattern * 

NSControl NSActionCell @selector Similar to Qt Signals & Slots

***Method Swizzling**  A technique for runtime modification of the relationship between message selectors and the implementation that will be called in response to a message * 

MethodSwizzling

***Delegation**  Enable one object to control, modify, or specialize the behavior of another object through a loosely coupled interface.

This is really an example of the Strategy design pattern.  In fact if you look at the "Application" algorithm implemented as a Strategy in Rob Martin's Agile Principles, Patterns, and Practices you see NSApplication + AppDelegate reproduced almost exactly.

http://www.stepwise.com/Articles/Technical/2000-03-03.01.html
http://developer.apple.com/documentation/Cocoa/Conceptual/CommunicateWithObjects/Articles/Delegates.html

