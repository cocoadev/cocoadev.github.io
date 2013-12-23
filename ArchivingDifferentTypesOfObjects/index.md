---
layout: page
title: ArchivingDifferentTypesOfObjects
---



I have seen plenty of examples of archiving, but all show only a single type of object being used.   I'm trying to save multiple objects used in my app, but it's not clear how to do this.

I took the RaiseMan example form the Hillegass book, and added a Department object (actually, an NSMutableArray of Departments, with an NSArrayController and NSTableView).
Each Employee object refers to a Department object, in a many to one relationship (many employees point to each department).

I can archive the employees, including each of their department information, using the basic techniques described in the Hillegass Cocoa book.    But, I'm not sure how I would archive the list of departments along with the employee data.


And, a related issue..  when restoring the archived data, can I retain the relationships between the department object associated with each employee, and the related department object in the departments NSArray? 
----
Archiving and unarchiving work with aribtrary graphs of interconnected objects just fine.  Think about the archives Interface Builder makes.  (A nib contains archived objects).

Just create a "root" object that contains at least one indirect reference to every object you want in the archive and archive the root object.  In the example you cite: "Each Employee object refers to a Department object", unless the Department object is conditionally archived, just archiving the Employee will cause the Department to be archived too.  Furthermore, the Department object will only be archived once no mater how many Employee's reference it, and when unarchived, all of the Employee's who originally referenced the same Department will still reference the same department.

However, it seems like what you are trying to do is nicely done for you automatically by Core Data.
----

Thanks for the response.   I added a 'Root' class, and allocated it in MyDocument's init, pointing to both the Employee and Department NSMutableArrays.  It seems to work fine.  Thanks!

I'll look into Core Data once I get a bit more comfortable with the fundamentals.  I've been extending the same app, with and without Bindings to get my Cocoa bearings.  I'll probably add a third variant, based on Core Data.

