---
layout: page
title: AddedContentSetGeneratesError
---



Hello!

I�m working with Core Data. I�m trying to set a relationship for an instance of an entity. I just option-dragged the  entity from Xcode to IB and I�m able to modify the relationship. No problem so far.

Now, I modified the ArrayController adding a contentSet which causes the tableView to display only those entities corresponding to a selected item (see representation below). When attempting to modify he relationship, I get this error:

�*** -observeValueForKeyPath:ofObject:change:context: only defined for abstract class.  Define -[NSKeyValueObservance observeValueForKeyPath:ofObject:change:context:]!�

The relationship doesn�t change every time, or sometimes it has changed when I reopen the app�

**Employee-Model representation of the problem:**
    
Department 1 > Employees A,B,C
Department 2 > Employees D,E,F
Managers > Alpha,Beta

Without contentSet (all instances of Employee): Employees A,B,C,D,E,F
With contentSet (Depart. 1 instances of Employee): Employees A,B,C

Without contentSet: Employee A, change manager from Alpha to Beta: OK!
With contentSet: Employee A, change manager from Alpha to Beta: erratic, logs error!

Let me precise that I used this type of contentSet from a relationship in ArrayControllers many times with no problem� However this time I concatenated two AC:

Entity1AC(no contentSet) [Works]

Entity2AC(contentSet: Entity1AC selection entity2s) [Works]

Entity3AC(contentSet: Entity2AC selection entity3s) [Doesn�t work]

Thank you for any help!!

--Flofl

----

