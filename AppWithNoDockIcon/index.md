---
layout: page
title: AppWithNoDockIcon
---

Question: How can I make it so my Application has no dock icon?

My application has a helper app with no interface, and it's supposed to just run in the background. But the helper has an NSTimer which I initiate in the main() method, and the dock icon bounces in the dock until the timer fires. How can I make it so it never has a dock icon?
Thanks.

----
See LSUIElement - This is one method which at launch time removes the item stating that it has a dock icon from the application's plist.

----
Another means is to create a Foundation tool in XCode and then manually add the Cocoa frameworks to the project, and import them in your header.  This is mainly useful for a UI-free helper tool which nonetheless needs access to higher-level AppKit functions (like NSImage or NSSound data, or the global NSApp object).

