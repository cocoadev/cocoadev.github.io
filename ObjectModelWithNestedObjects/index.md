---
layout: page
title: ObjectModelWithNestedObjects
---



In all the basic projects I've done thus far, I've used single objects,  using that object's instance variables for the NSTableViews.

Now, I'm trying to add another custom object, which contains related information.  For example,  my main class is BankAccount, which contains accountNumber, balance, accountHolderName, etc.   That works easily in bindings and non-Bindings projects I tried.   Next, I added an object Branch into each BankAccount.  Branch contains address,phoneNumber,manager,hoursOfOperation, etc.


I thought I could just refer to the iVars in my Branch objects by key path.  So, in a non-Bindings NSTableView, an identifier could be  branch.manager to show the manager of the branch an account is at.   Or, could similarly be used in Bindings as the key path.    But neither are working for me.    In the non-Bindings app, I'm getting an error saying branch.manager is not KVC compliant.   With Bindings, I'm not seeing an error message, but it's not working..

Is there some trick to doing this?

-------------------

After monkeying with it a bit more, I found that the syntax was correct.    "branch.manager" works, I think the problem I was having was memory management related.

