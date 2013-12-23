---
layout: page
title: HowToUseTwoTableViewInOneWindow
---

Describe HowToUseTwoTableViewInOneWindow here.

I need to use two table view in one vindow ... how i do ?
Tanks anyway 

----

Create two separate classes and use each one as a data source for one of the table views.
Also, you could just use one class and have the data source methods check to see which table view is the "sender",
returning the appropriate value for the appropriate table. --Goldfish

----

Tks Goldfish ... i try now !

