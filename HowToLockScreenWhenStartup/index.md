---
layout: page
title: HowToLockScreenWhenStartup
---

Here is my question:I need to lock the screen at the system startup, the user must submit his authentication information to a server to use the machine.But I really have no idea how to do this.A screen saver can help with this or cocoa got a better support??

----

On Mac OS X you can require the user to login by disabling the following checkbox: "System Preferences > Accounts > Login Options > Automatically log in as:"

If you want your application to request authentication details, take a look at: http://developer.apple.com/documentation/Security/Conceptual/authorization_concepts/index.html

-- GrahamMiln

----

