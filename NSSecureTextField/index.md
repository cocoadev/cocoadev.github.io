---
layout: page
title: NSSecureTextField
---

http://developer.apple.com/techpubs/macosx/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSSecureTextField.html

----

Inherits from NSTextField.

A NSSecureTextField is like a normal NSTextField, except that the text can be replaced by bullets (for privacy), the text is not copyable, and SecureKeyboardEntry mode is entered whenever it becomes the first responder (and restored to its previous setting when it resigns that status).

An obvious application for this Class is a password field.

Not present on any of the standard InterfaceBuilder palettes, but you can instantiate a normal NSTextField and set its custom class to NSSecureTextField using the Custom Class inspector (cmd-5).

----

Unfortunately, as of MacOSX 10.3 ("Panther"), NSSecureTextField does not support input of characters via unicode keyboards. You cannot have, for instance,  the 'US Extended' keyboard layout selected when typing into an NSSecureTextField. This means that if users are going to be typing international characters available only through those keyboards, you will have to do your own implementation of a secure password field.

