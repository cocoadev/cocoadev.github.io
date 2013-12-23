---
layout: page
title: SelectingMenuItems
---

I have a document based project with a standard toolbar on the document. I want an item on the toolbar to choose a menu item. I will say that the outlet is called savemenuitem for the menu I want to select. This menu item then performs an Applescript. Any ideas?

thanks,
scott (website: http://chs.claremont.edu/~ssmith)

----

Since menu items are target-action based, the easiest way would just be to set your toolbar item to have the same target and same action as the menu item.  Or are you trying to select a choice in a popup? -- Bo

----

No, since the menu item runs an Applescript Studio script. So I don't want the code in the project twice to slow things down.

-scott

