---
layout: page
title: AddressBookTroubleshooting
---

**Problem:** I've included <AddressBook/AddressBook.h> but when I try     NSArray *people = [book people];, the array is empty.

**Solution:** Remember to add the AddressBook.framework to your project. Including is not enough.

----

