---
layout: page
title: CocoaAppsWithoutMenuBarOrDock
---



What is the easiest way to have an app that doesn't display any menu bar or it's icon in the dock, only a status item. I can do the the status item. I just need to know how to do the rest.

Jacob 

----

Easy to do. In project builder, goto the 'Project' menu, select 'Edit Active Target "Project Name"'. Select 'Expert View' out of the 'Info.plist Entries'. Add to this list, LSUIElement with a number value of 1.


Enjoy!


-- MatPeterson

----

Thanks man, works great.

Jacob

