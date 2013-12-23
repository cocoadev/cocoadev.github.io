---
layout: page
title: CodaLikeNSToolbar
---

Can anyone explain how to create a toolbar like the one we find in Coda for instance? There are many apps using this type of toolbar but I can't find no information on how to create one. For those who don't know what I'm talking about, it's a toolbar with toolbar items that are used to switch views, like the toolbar used in a preference pane.  

----
Searching "NSToolbar preferences" on this site gives you some not-too-bad results. The NSToolbar page itself has a bit of information (the basic idea is to use selectable toolbar items coupled with a tablessNSTabView). There's a bit of discussion and an implementation at MultiPanePreferences. And Dave Batton has wrapped most of the functionality into a nice class, the most recent version of which can be downloaded from http://www.mere-mortal-software.com/blog/details.php?d=2007-05-31 (There's a link on that page to the original post where he explains how to use it.)

