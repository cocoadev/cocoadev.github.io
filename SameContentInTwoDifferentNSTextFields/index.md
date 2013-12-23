---
layout: page
title: SameContentInTwoDifferentNSTextFields
---

I have two NSTextFields, and I would like for them to have the exact same content. The only way I've been able to do this is either with the controlDidEndEditing: delegate method or with the takeStringValueFrom: action in Interface Builder. The problem I have with both of these is that the content only updates after the user has finished editing the text field, and I would like the second text field to have the same as the first even when it's being edited. I tried the controlDidChange: delegate method, however, when I try to get the value from the first text field (I'm using [firstTextField getStringValue]) the first text field ends editing and I'm unable to enter more than one character. I tried searching for this and didn't come up with anything, I also tried taking a look at the the Field Editor's documentation but I couldn't figure out a way to do this.

----

This is very easy to accomplish with Bindings, by simply binding both text fields to the same key in the same controller. Even if you don't want to use bindings for the rest of your controller code, you could probably still do it by making an NSObjectController just for this purpose and then leaving the rest of your code as-is.

----

I tried biding it to a NSObjectController like you suggested, but I keep having the same problem, the second text field only changes its content after the first one has finished editing. Also, there's another part of my program where I want to do something similar, but instead of copying the exact value, I'd like to modify it, for example, I have a field which contains a number and I'd like to be able to multiply that number by another and put the result in another text field, again even while the first field is being edited.

----

Check the "continuously updates value" checkbox in the value binding for the text field.

----

That works, however bindings doesn't seem to be the option I'm looking for. I want the changes to the first text field to affect the second text field, but not the other way around, if I change the second text field, the first text field should remain the same. Also, the other problem I had (the one where the first text field has a number and the second one has the result of that number being multiplied) does not seem to be doable with bindings.

----

Set one of your objects as the delegate of the first field.
Search the docs for textDidChange:.
BjoernKriews

----

textDidChange: is the delegate method from Field Editor from the text field, the correct delegate method would NSControl delegate method controlDidChange: However, when I call [firstTextField stringValue] in that method to get the value and set it in in second text field, it commits the edit so I can not input more than one character in the first field. I'm not sure if this has anything to do, but my first field has a formatter.

*Actually, it's     controlTextDidChange: (see NSControl). Instead of calling     stringValue, try getting the field editor from the NSNotification (key     @"NSFieldEditor" in the     userInfo dictionary) and getting the text from there with the     string method. --JediKnil*

----

That works, thanks.

