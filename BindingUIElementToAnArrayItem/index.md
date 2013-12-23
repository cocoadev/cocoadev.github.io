---
layout: page
title: BindingUIElementToAnArrayItem
---

How do I bind the value of an NSTextField to a plist array item? The plist in question is my application defaults dictionary (in ~/Library/Preferences).
I have a bunch of text fields in a window and would like to map their contents to key-value pairs contained in the plist array, either in UI Builder or programmatically. I'm not sure how to specify the array index in the keypath either...

Can anyone help? Thanks!

----

First, please do not use this wiki to post MailingListMode style messages. If it's a simple Q&A that reads like it should be posted in forums or a mailing list, post it to the cocoa-dev list at www.lists.apple.com. 

Second, The text field is your view. The data in your plist is your model. If you read the introductory documentation on Cocoa (specifically, look for "Model View Controller"), you'll see that you're lacking the middle component: the controller. Typically, you achieve a general connection by using an NSArrayController. The text field's *value* would be bound to *a property* of *the arranged objects* of the array controller in question. Alternatively this could be bound to the *property* of the *selected object* of the array controller (ie, one member of your array).

Assuming the model is KeyValueCoding and KeyValueObserving compliant (more documentation you **must** understand), changes to the model will (via the controller) automatically update the view and vice-versa. In short, you have a lot of reading to do to understand the basics before you can hope to leverage Cocoa Bindings (an advanced Cocoa topic). The basics are: The MVC design pattern Cocoa follows and Key Value Coding / Key Value Observing. If you don't understand these, nobody here will be willing to help you because you haven't done your homework.

Third, to get to things stored in user defaults, you shouldn't be worrying about the plist file and where it's stored. You're short-circuiting the preferences mechanism (potentially leading to data loss and lots of confusion) by not using Cocoa's built-in NSUserDefaults and (in the case of Bindings) the NSUserDefaultsController. For example, you'd have an NSUserDefaultsController and an NSArrayController in your case. The array controller's contents would be bound to a property key in the defaults controller's values (the key under which your array is stored) and then the details outlined in the "second" point above will do the rest. 

Additionally, you may find this helpful: http://www.cocoadevcentral.com/articles/000080.php  ...  don't assume you'll be able to create a working app that's worth anything by simply following and trying to adapt the lesson it teaches without actually understanding the mechanisms behind it. You still have to know the above-mentioned topics fairly well to make anything useful. Without it, best to write your app without Bindings as it can create far more work than its worth if not properly leveraged (or just plain not a good fit). I've abandoned the use of Bindings on half of the projects I've attempted to use the technology with simply because it required me to bend just a bit too far to make it happy when simpler, more traditional methods (which, granted, required me to write code) worked a lot better with a lot less effort.

