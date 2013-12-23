---
layout: page
title: ABRecord
---

ABRecord is a child of NSObject
----
an ABRecord object represents a row in the AddressBook database.


    - (id)valueForProperty:(NSString *)property;

    * Returns the value of a given property. The type of the value depends on the property type.
    * The retuned object is immutable (NSString not NSMutableString, ABMultiValue not ABMutableMultiValue, etc..)
    * Raises if property is nil




    - (BOOL)setValue:(id)value forProperty:(NSString *)property;

    * Set the value of a given property. The type of the value must match the property type.
    * Raises if property is nil or value not of the correct type
    * Returns YES if the value was set successfully




    - (BOOL)removeValueForProperty:(NSString *)property;

    * Remove the value of a given property. Subsequent calls to -valueForProperty on the same
    * property will return nil.
    * Raises if property is nil
    * Returns YES if the value was removed successfully


Category:PointlessInformation‏‎

