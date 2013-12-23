---
layout: page
title: BindingToANSTextFieldWithNSUserDefaultsController
---

Hi,

I am trying to use the NSUserDefaultsController class to bind a NSTextField to the shared user defaults. In the init method for my app, I create a NSDictionary with a single entry that associates a key called colorKey with an NSString (the color). I then register the defaults by calling registerDefaults, and passing the NSDictionary as an argument.

The settings for the NSTextField in Interface Builder that I want to bind are:

value:
bind to: NSUserDefaultsController
Controller key: values
Model key path: colorKey

When the app loads, the NSTextField is empty. Changes to the NSTextField are not changed when I quit the app. I've wasted more time than I care to admit on this exercise, so if anybody knows what I am doing wrong/not doing, please let me know.

Thanks

The value will only be set in the dictionary when you exit the text field. Maybe you should check "Continuously sets value" in the Binding settings (I'm guessing that is your problem).

