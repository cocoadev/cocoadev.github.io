---
layout: page
title: ABMutableMultiValue
---



ABMutableMultiValue Inherits from ABMultiValue, NSObject
----


    - (NSString *)addValue:(id)value withLabel:(NSString *)label;

 *Adds a value with its label
 *Returns the identifier if successful. nil otherwise
 *Raises if value or label are nil
 *Note: No type checking is made when adding a value. But trying to set a multivalue property with a multivalue that doesn't have all its values of the same type will return an error.


    - (NSString *)insertValue:(id)value withLabel:(NSString *)label atIndex:(int)index;

 *Insert a value/label pair at a given index
 *Returns the identifier if successful. nil otherwise
 *Raises if value or label are nil or the index is out of bounds.
 *Note: No type checking is made when adding a value. But trying to set a multivalue property with a multivalue that doesn't have all its values of the same type will return an error

    
 
    - (BOOL)removeValueAndLabelAtIndex:(int)index;

 *Removes a value/label pari at a given index
 *Raises if the index is out of bounds

        

    - (BOOL)replaceValueAtIndex:(int)index withValue:(id)value;

 *Replaces a value at a given index
 *Raises if the index is out of bounds or the value is nil

        


    - (BOOL)replaceLabelAtIndex:(int)index withLabel:(NSString*)label;

 *Replaces a label at a given index
 *Raises if the index is out of bounds or the label is nil

        


    - (BOOL)setPrimaryIdentifier:(NSString *)identifier;

 *Sets the primary value given its identifier.
 *Raises if identifier is nil
 *Returns YES if successful


Category:PointlessInformation

