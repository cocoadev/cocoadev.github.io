---
layout: page
title: ChangeFirstResponderFromATextFieldSActionMethod
---

At the end of my textField�s IBAction method, I�d like to make another textField firstResponder�

Sounds easy, but�

I tried makeFirstResponder:, selectNextKeyView: and selectKeyViewFollowingView:, but the sender always remains firstResponder.

I put the code in another method I�m calling with a performSelector: I can see the new textField become firstResponder for a second, then the old one takes the focus back!! (Same result placing the code in a button�s action with the Return key equivalent�)

So it seems that while we are in the action method, the sender refuses to resign its firstResponder status, I tried to override resignFirstResponder but nothing� (Maybe I didn�t do it in the correct way�)

I�m entering numbers in various textFields, so I find it easier to validate the entry with Return than with Tab, but I�d like to be ready to enter other numbers in the next textField without having to tab!!!

----

It seems that you�ve got to check Enter Only in IB and not End Editing. That way makeFirstResponder and selectKeyViewFollowingView both work. selectNextKeyView doesn�t work but this might be due to something easy to fix(?).

----

You might need to override     keyDown: in a NSTextView subclass (or category if you're adventurous), set it as the window's field editor, and check for the return key and not pass that event up to super.

----

How do you set it to be the window's field editor?

*implement*     - (id)windowWillReturnFieldEditor:(NSWindow *)sender toObject:(id)anObject *in your window's delegate and return your custom class. Also, you need to make an NSTextView subclass, not an NSTextField subclass as I said above, sorry. I changed it above as well.*

*Look at  the source for DTCPathView (in ObjectLibrary) for an example of how to do it.*

I once succeeded in catching the Return key, but it seems that pressing shift- or command-Return does nothing, whereas option-Return works� Any idea why this behavior occurs?

----

Tentative solution: A working example with code is posted in ReturnInsteadOfTab.

I made 2 columns of 4 texFields and am now able to tab horizontally through them (normally using Tab: 0-1-2-3-4-5-6-7-0�) or vertically (using Return: 0-2-4-6-0� or 1-3-5-7-1�).

I created a custom object that stores every TF�s next and previous responder.
An array of such objects is build in the controller�s awakeFromNib.
You just cycle through that array to find the current view�s next "return-responder", recursively if it turns up to be invisible�

----

I have a similar problem. A simple data entry window with 3 text fields, an �Enter� and a �Finish� button. The Enter button has its key equivalent set to Return. The action is in a subclass of NSWindowController, like so:

    

- (IBAction) pressEnterButton: (id) sender;
{
   if ([myDataController validateField1:[field1 stringValue]
                              Field2:[field2 stringValue]
                              Field3:[field3 stringValue]]) {
           [myDataController createWithField1:[field1 stringValue]
                                    Field2:[field2 stringValue]
                                    Field3:[field3 stringValue]])
           [field1 setStringValue:@��];
           [field2 setStringValue:@��];
           [field3 setStringValue:@��];
           [[self window] makeFirstResponder:field1];
          }
}



Now if you click the Enter button with the mouse, the result is exactly as intended: the three fields are copied to the data controller, emptied, and focus is sent back to the beginning.

If the Return key is used the result is exactly as above: data is copied and the fields are emptied ok, but then the new field1 takes the focus for a second, then field2 or field3 takes it back.

I don�t mind so much when I get huge bring-down-the-system bugs in my program, but this is one of those annoying little things that makes me want to [computer applyAxe]

----

Have you tried something like     [window performSelector:@selector(makeFirstResponder:) withObject:field1 afterDelay:0]?

----

That works! I'm quite pathetically grateful.

