---
layout: page
title: NSObjectController
---

 

**See:** http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSObjectController_Class/index.html

----

NSObjectController is a controller class compatible with CocoaBindings. Properties of the content object of an instance of this class can be bound to user interface elements to access and modify their values.

By default the content object of an NSObjectController instance is NSMutableDictionary. This allows you to use a single NSObjectController instance to manage many different properties referenced by key value paths. The default content object class can be changed by calling setObjectClass:, which subclassers must override.

