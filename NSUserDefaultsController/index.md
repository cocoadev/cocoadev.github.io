---
layout: page
title: NSUserDefaultsController
---



From the docs: *NSUserDefaultsController provides a layer on top of NSUserDefaults and allows you to bind attributes of user interface items to the corresponding key in an application�s user defaults.*

[http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaBindings/Concepts/NSUserDefaultsController.html]

A *shared instance* of the class is available via     [ NSUserDefaultsController sharedUserDefaultsController ] and the data model managed by this instance is the usual instance of NSUserDefaults that you can obtain with     [ NSUserDefaults standardUserDefaults ]. This shared object is available via the Bindings inspector for a control when using InterfaceBuilder to construct the ApplicationPreferences interface.

Typically it is most useful when you can bind controls in your preferences interface directly to a default value. Choose *Bind to: Shared User Defaults* using *values* as the Controller Key and a Model Key Path that corresponds to the key in your app's PropertyList. Of course, this can also be done with Manual Bindings (see example in the documentation referenced above.)

As with the traditional approach to managing your defaults property list without the use of CocoaBindings, there are choices to be made about when to save or synchronize the application's current set of preferences, how to make sure that the defaults are properly initialized with "factory defaults" even if the application's property list is discarded, or how to restore that set of "factory defaults" if the user chooses to do so.

When you attempt to retrieve a value from your current defaults set the search proceeds (according to the documentation):


* (1) The value for that key in the *key-value-compliant* object returned by     [ sharedUDCInstance values ]
* (2) The value for that key in the NSUserDefaults instance returned by     [ sharedUDCInstance defaults ]
* (3) The value for that key represented in the initial values established for the shared instance


The default behavior of the NSUserDefaultsController is to apply any changes immediately. Nevertheless, the documentation reminds you that the     setInitialValues: method should not be considered a substitute for sending the     registerDefaults: message to the shared NSUserDefaults instance.

The documentation also gives this handy tip: *The NSUserDefaultsController automatically provides notification of (a) value change to any established bindings for (a particular) key path.*

----

**Discussion, Q-and-A**

How do I bind NSUserDefaultsController to NSArrayController or NSObjectController?

I've gone through the tutorials on bindings and I bascially get the concepts but I'm falling down when it comes to binding anything but a simple view (textfield, label, slider) to the NSUserDefaultsController.

I have an NSArrayController which is bound to an NSObjectController as are the columns of a NSTableView. In turn the NSObjectController is connected through its content outlet to a normal NSController. Everything works fine, the table add/remove buttons are bound to the NSObjectController and they add and delete as they should but I want to be able to save the the contents of the table to the application's property list. I can't figure out how to bind the NSObjectController to the NSUserDefaultsController and have this work.

Is it possible? Do I just have to give in, create an 'Apply' button and code the NSUserDefaults manually? 

----

For some sample code that accomplishes this, see this thread: http://www.cocoabuilder.com/archive/message/2004/11/6/120927

[Linking to cocoabuilder.com is not a great idea since it can (and did) disappear. In lieu of a link to lists.apple.com, I asked archive.org and it said the gist of the message in question was a link to ftp://ftp.ritual.org/MacOSX/Cocoa/ArrayInUserDefaults.zip but the gist of *that* is that, as of Tiger, the key is setting "Handles Content As Compound Value" on your NSArrayController.]

----

I'm building an application with multiple preference panes and lots of options that I don't particularly want floating around in the root level of the UserDefaults plist file; is it possible to access a collection type with Bindings; i.e. seems easy attaching an NSArrayController to the Shared User Defaults, but having a dictionary with keys seems more difficult, as it doesn't seem that dictionaries conform to KVC or KVO thus making it impossible for Bindings to retrieve and set the values. 

----

It sounds as though you could create normal NSObjectController instances with NSMutableDictionary object class names which are bound to the user defaults.  That way only the key that you are using to bind each of these controllers would be in the root of the file and each of these keys would then resolve a sub-dictionary within the plist file.

----

I have an NSObjectController (myObjCont) of type NSMutableDictionary with its content outlet pointing to the Shared Defaults instance. Then I have a NSTextField with its value bound to myObjCont / selection / testEnable. I get this exception:

     2005-01-10 14:11:00.684 Flashcards[9116] *** Uncaught exception: <NSUnknownKeyException> [<NSUserDefaultsController 0x396110> valueForUndefinedKey:]: this class is not key value coding-compliant for the key testEnable.


I was just testing to see if I could bind an object this way, but I'm either hooking it up wrong or it's not possible to use the Shared Defaults instance in this manner.

----

You cannot bind directly to the sharedUserDefaultsController object. Rather, you must bind to the KVC-compliant object returned by its values method. In IB, that means setting the Controller Key to values and the Model Key Path to your preference name. Programatically, that means calling the extra method before accessing or mutating keys, such as [[[NSUserDefaultsController sharedUserDefaultsController] values] setValue:myValue forKey:myKey]

----

GNUstep does not recognize this class. Just so you realize when you step off the portability cliff. Those poor non-mac users.

----

I'm sure that was correct when you wrote it, but as of 0.12.0 it does:
http://www.gnustep.org/resources/documentation/Developer/Gui/ReleaseNotes/ReleaseNotes.html

