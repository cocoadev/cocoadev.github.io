---
layout: page
title: BindingsInspector
---



See http://developer.apple.com/documentation/Cocoa/Reference/CocoaBindingsRef/index.html.

The Bindings inspector is divided into functional areas, called *binding groups*. Within each binding group are attributes of the *interface object* (e.g. NSTextField), called *bindings*. These bindings can get their values from *model values* (e.g. an NSString in your document), or set the model values to the bindings' own values. Each binding has:

*A *Bind* checkbox. If checked, this attribute is bound.

*A *Bind To* drop-down. This lists various NSController<nowiki/>s that can supply model values. It lists three default controllers plus any controllers instantiated in this NIB.

*A *Controller Key* combo box. An NSController controls a model value's UI-related attributes. This combo box selects which UI attribute of the model value will set (or be set by) this binding.

*A *Model Key Path* combo box. Each NSController is associated with a *content object*, which is one or several *model objects* (a document-related class). The content object can have several model values. This combo box selects which model value the above controller key applies to. This combo box actually contains a key which is used to look up the content object's values via KeyValueCoding.

*A *Value Transformer* combo box. The value transformer is an object that converts from one type of value to another type of value. Value transformers are subclasses of NSValueTransformer. For example, if a particular model accessor returns an NSString, a transformer might convert it to an NSNumber. When a UI element is bound to a particular model attribute via an NSController, it may specify that the value fetched from the model is converted to another data type that is compatible with the UI element. There are a few built-in transformers, a particularly useful one is NSIsNotNil.  This particular transformer converts a Nil/Non-Nil value returned from a model accessor into a BOOLean NO/YES value. A use for this transformer is in the "enabled" binding, where you can subscribe to a field value, and depending on whether it has a value, enable or disable a different, but related, UI element. [ alephx01 (at) mac (dot) com ]

*Assorted checkboxes and other controls:


**Raises For Not Applicable Keys*. Raises an exception if the key is the wrong type.

**Validates Immediately*. If unchecked, validation occurs when the control resigns first responder.

**Conditionally Sets Editable*. Will not be editable if the value is a multiple selection.

**Conditionally Sets Enabled*. Will not be enabled if the value is a multiple selection.

**Multiple Values Placeholder*.  If the binding returns an array (which would display as "(item1, item2, item3)") and you want to replace this with a helpful message instead, simply place the message (e.g. "multiple items selected") in this placeholder box.  A common reason for arrays appearing is (e.g.) in the AdvancedCurrencyConverter application when multiple conversions are selected in the list box; the converter fields at the top will then try to display multiple items.  An alternative approach here is to use a custom ValueTransformer to (e.g.) show the sum of the array.

**No Selection Placeholder*.  You can supply a string here to be displayed as the value when no items are selected in a list.

**Not Applicable Placeholder*.  You can supply a string here to be displayed if the key is the wrong type.

**Null Placeholder*.  You can supply a handy string to replace a NULL return value from a binding.  For instance, if some of your objects do not have proper values for all fields, then they can return NULL, and the interface can replace NULL with a more user-friendly (and localised!) string, e.g. "Not supplied".

*For others, see http://developer.apple.com/documentation/Cocoa/Reference/CocoaBindingsRef/Concepts/BindingsOptions.html.



----

InterfaceBuilder effectively calls the following code for each binding:
    
[**interface object** bind:@"**binding name**" toObject:***Bind To*** withKeyPath:@"***Model Key Path***"
options:**Dictionary of *Value Transformer* and placeholders**]];

or
    
[**interface object** bind:@"**binding name**" toObject:***Bind To*** withKeyPath:@"***Controller Key***.***Model Key Path***"
options:**Dictionary of *Value Transformer* and placeholders**];


----

The bindings listed in the Bindings palette come from the NSKeyValueBindingCreation protocol. Each binding has a name and a value class, which presumably can be set and discovered programatically. Most, but not all, bindings correspond to methods of the class. Bindings are not KVC keys.

Bindings like hidden, hidden2, hidden3 are OR'd or AND'd together to get the final state. *hidden* is OR'd, *editable* is AND'd, *enabled* is AND'd.

For documented bindings, see http://developer.apple.com/documentation/Cocoa/Reference/CocoaBindingsRef/index.html. Note that the bindings listed for a control will include the control's superclasses, and may include bindings from an NSCell as well.

----
Some other tips and tricks: http://homepage.mac.com/mmalc/CocoaExamples/controllers.html

----

Why is it that this page, in particular, always gets nailed by spam? :(

