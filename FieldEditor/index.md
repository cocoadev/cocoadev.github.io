---
layout: page
title: FieldEditor
---




The field editor is an NSTextView which is maintained by NSWindow. The field editor replaces any NSTextField or NSTextFieldCell in the window for editing purposes. It does not replace other NSTextView<nowiki/>s.


* CustomFieldEditor has working example code derived from discussion at: CreatingCustomFieldEditor
*FieldEditorIssues �Dynamically resizing the field editor.
*FirstResponderTrouble �Catching the return key.
*http://developer.apple.com/documentation/Cocoa/Conceptual/TextEditing/Tasks/FieldEditor.html �All about the field editor.


The field editor can be customized on a per-control basis by implementing **windowWillReturnFieldEditor:toObject:** in the NSWindow's delegate.

Note that although the field editor replaces NSTextField, it is an NSTextView, and you can call NSTextView methods.

The field editor maintains selection. If you want a text field to keep its selection when it is not actively being edited, you must not let that text field use the field editor.

----
**How the Field Editor Works**

Basically, each window has one text view that handles all the text input.  This is to avoid bloat, we all think of NSTextView's as heavy, and NSTextFields as "light", with the truth being that only the NSTextView can handle text since it is part of the entire Cocoa text system.  Now imagine if every NSTextField had to take up the duties of an NSTextView, or even worse, had its own copy of an NSTextView.  Here it becomes obvious why there is only one that is shared.

What happens:


*1. You click on a control (or double click).
*2. It requests the field editor (which may be replaced by a delegate as described above).
*3. The control edits the field editor to its liking, setting itself as the delegate.
*4. From what I have witnessed, in many places (such as NSTableView) is is placed in an NSClipView private subclass (with no enclosing scroll view), which is why you can type larger than the area and then move back.
*5. This clip view is then added as a subview to the NSControl, thus showing up on top of the view and making it appear as though you are typing directly into that view.
*6. You finish typing, the NSControl synchronizes the values (or rejects it), and removes the clip view (and thus nstextview) from its subviews.


I hope this gives you a pretty good idea of what is going on -FranciscoTolmasky


----

How do you make a text field not use a field editor at all?

----
AFAIK you don't. That's just how text fields work. If you want to maintain selection state, save the range in an ivar.

----
Can you make it use itself as the field editor, or will that cause some sort of horrible recursion? 

----
No, because there is nothing in itself that can be used as an editor. When a text field gets focus, the window inserts the field editor for that control. That's just the way it works.
 
----
Is it a problem if you don't use an NSTextView as the field editor?

----
From the link above:

"You can override the NSWindow method in subclasses or provide a delegate to substitute a class of text object different from the default of NSTextView, thereby customizing text editing in your application."

----
...but it still has to be a subclass of NSText, right?

----
Maybe, but not necessarily.

----
I tried creating my own field editor, but I ended up hacking an NSTextView (to be used) -- there's just too many assumptions in the Cocoa field editor system about how things works. It's OO, but in my experience not very extensible.

