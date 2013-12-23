---
layout: page
title: StoringNumbersInACalculator
---

I am new to Cocoa and I am developing a Calculator. My problem is this: how can I store the numbers which the user enters by clicking the buttons? For example a user enters 545.5 using the Calulator's buttons. Now how do I store this number? I can store the most recent number the user pushed but then only 5 will be entered. Please advise. - Saad R. Abbasi

----

Just do:

double lhs= [myTextField doubleValue];

after plus is pushed or whatever.

- General/FranciscoTolmasky

----

Thank you for reply. Yes well I could do that but how do I check for only 1 occurrence of '.' in the General/NSTextField? Do I dump the contents of the General/NSTextField into a variable and perform searches on that? Is there a better way? -- Saad R. Abbasi

----

Attach a General/NSFormatter to the text field. Drag it onto the field from IB's Attributes palette.
