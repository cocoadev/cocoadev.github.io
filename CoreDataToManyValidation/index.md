---
layout: page
title: CoreDataToManyValidation
---



I have an application with a window as such:

http://www.aphrodite.com/~paulf/shots/shot1.gif

All columns bindings have "Validates Immediately".  The problem is the only one that actually does validate when changed (vs when the document is saved) is the "Factor" column.  None of these fields allow null values, yet I can enter null values for any other field and the application doesn't complain until the user saves the document later.  It's my understanding the "Validates Immediately" binding option affects this.  Why is it only working for that one field?

