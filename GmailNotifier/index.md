---
layout: page
title: GmailNotifier
---

Google's first MacOSX application, available at http://toolbar.google.com/gmail-helper/index?promo=gdl-en.

Interesting things about it from a Cocoa developer's point of view:



* Google has chosen **GG** as its own Objective-C prefix (see ChooseYourOwnPrefix).
* Plug-ins can be created as LoadableBundle***s that are placed in ~/Library/Application Support/Gmail Notifier or /Library/Application Support/Gmail Notifier. See the GGPluginProtocol.h header in Gmail Notifier.app/Contents/Headers for more information. (Hmmm... I'll try my hand with it and the Growl framework if I have time.)



 -- EmanueleVulcano aka millenomi

