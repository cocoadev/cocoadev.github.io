---
layout: page
title: ChangingDynamicallyPopUpMenu
---

I was wondering wether one can change dynamically the content of a pop-up menu and/or table view.

Here is the problem:

Let's say I have an Employee entity with an attribute "name".  I also have a relationship "reportTo" (employees usually report to other employees, but in some cases they report to themselves).

"reportTo" is chosen with a pull-down menu, displaying all the employees ("name").

What I want is that the pull-down menu displays all the "name"s with the exception of the "name" of the selected employee.  Here I want to display "self".  Of course if you chose another employee, the "reportTo" pop-up menu has to be changed so that the former employee is again displayed with his/her propper name, and the now selected employee displays "self".

Any suggestion how to do that elegantly (without having to code the pop-up menu programmatically) ?

Thanks in advance, Michael

----

One way to do this is to edit the popup's bindings in Interface Builder, checking "Allows Null". If the reportsTo relationship is null, null is selected in the popup. You can specify (in the bindings settings) what the popup will display if the selection is null (in your case, "Self"). This assumes that, by your application's logic, having *NO* reportsTo relationship always means the employee reports to self.

