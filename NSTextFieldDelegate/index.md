---
layout: page
title: NSTextFieldDelegate
---

NSTextField docs - http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSTextField.html

NSTextField does not define any new delegate methods of its own, but the delegate can still customize behavior using delegate methods inherited from NSControl.  These are:

    
� control:isValidObject:

� control:textShouldBeginEditing:
� control:textShouldEndEditing:
� controlTextDidBeginEditing:
� controlTextDidChange:
� controlTextDidEndEditing:

� control:didFailToFormatString:errorDescription:
� control:didFailToValidatePartialString:errorDescription:

� control:textView:doCommandBySelector:

� control:textView:completions:forPartialWordRange:indexOfSelectedItem: *(10.3 and later)*



One source of confusion is the role of the text field in the editing process, and the methods labeled "Text delegate methods" in the NSTextField documentation.  These are not delegate methods offered by the text field, these are methods *implemented* by the text field.  When the user edits a text field, a shared FieldEditor does all of the work.  This is an NSTextView object by default, and the text field is its delegate.  The "text delegate methods" implemented by NSTextField customize the behavior of the FieldEditor. 

See also

