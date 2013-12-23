---
layout: page
title: PasswordAssistant
---

The Password Assistant is the window that tells you the security of a password entered into an NSSecureTextField. In 10.3, it features a numeric indicator (from 0-128, though it can go higher for very long passwords), and a bar (like a progress bar) that is red for insecure passwords up to green for very secure passwords. It also provides tips such as 'This is a word in the dictionary', 'This is too similar to your old password', etc. In 10.4, it can also generate random and memorably-random passwords.

The Password Assistant can be accessed through two Apple applications:


* The Keychain Assistant provides the Password Assistant in its 'Change Password for Keychain' panel.
* The Disk Images Helper (invoked by hdiutil and Disk Utility) provides the Password Assistant in its panel for setting the password on an encrypted disk image.


In both cases, the Password Assistant is accessed by clicking the round 'i' button (10.3) or rectangular button with a key icon (10.4) in the lower-left corner of the password panel.
 http://www.le-meilleur-forfait.com http://www.releve-identite-operateur.fr https://twitter.com/Meilleur http://www.obtenir-rio.info http://www.portabilite.info
----



Here's how to use the Password Assistant in your own app:


* Use ClassDump on SecurityInterface.framework.
* Delete all the classes from the dump except SFPasswordAssistantInspectorController.
* Make sure that showPasswordAssistantPanel: is an action, and that _baseWindow, _originalPassword, _newPassword, and _verifyPassword are outlets.
* Save the dump as a file (if you have not done this already).
* Drag the dump file into your nib window.
* In InterfaceBuilder, instantiate SFPasswordAssistantInspectorController.
* Instantiate at least one NSSecureTextField (this is the 'new password' field). If you want, create the other two as well.
* Connect your password-assistant button (the 'i' button in the apps named above) to the SFPasswordAssistantInspectorController instance's showPasswordAssistantPanel: action.
* Connect the relevant _fooPassword outlets of the instance to your NSSecureTextField<nowiki/>s. You need at least _newPassword.
* Connect the _baseWindow outlet to the window that hosts your 'i' button (or at least contains the text field(s)).
* In XCode, add SecurityInterface.framework to your project.


Unfortunately, InterfaceBuilder doesn't seem able to determine what frameworks you have set up in Xcode, so you won't be able to use the Test Interface command with this (IB will log a message saying that there is no SFPasswordAssistantInspectorController class and that it will use NSObject instead, and the 'i' button won't work). You will need to build the application and test within it.

*--boredzo*

----
I have an example implementation, with source, as Password Assistant: http://www.codepoetry.net/products/passwordassistant
*--codepoet*

----
I find that highly amusing (and cool), considering that I too wrote an example implementation -- in fact, that's how I discovered everything above. :)

One of these days, I should update my implementation for Tiger and put it online again. It was for awhile, but then my old web host went away.

*--boredzo*

----
I found codepoet's example on github, which I forked and adopted to an agent-style app with only a status bar icon to invoke the password assistent.
I only tried building/running it using the lastest developer previews of Xcode (4.4) and OS X (10.8), so there might be issues with older versions.
https://github.com/martijnthe/Password-Assistant

*--martijnthe*

