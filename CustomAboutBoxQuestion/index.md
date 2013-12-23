---
layout: page
title: CustomAboutBoxQuestion
---

I want to create a custom About Box using its own NIB in a Cocoa app that only has a custom NSView subclass.  Looking at "Cocoa Applications" from O'Reilly, on p.192, they give a procedure for doing it when there is a Controller in the app (Controller.h/.m).  If I just have the NSView code (.h/.m) - can I follow same procedure but work within the custom view files or must I use a controller?  Thanks

bp

----

I don't think you can -- imagine you close the main window and select about from the menu, which object will receive the message (to open the about window)? For this reason you need a controller.

well, you could actually bind the about menu action directly to the NSView object, but ehm... that's rather clumsy design.

----

I disagree; it seems easy enough. In the same NIB file as the main menu, create the about box, and put your view in there. Connect the About menu item to the about box's makeKeyAndOrderFront. Voila. If you need to initialize the view before-hand, implement awakeFromNib in either the NIB's owner or the view itself.

Maybe I'm not understanding what you mean, though. �DustinVoss

