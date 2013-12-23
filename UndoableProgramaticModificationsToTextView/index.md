---
layout: page
title: UndoableProgramaticModificationsToTextView
---

I need to modify the contents of my text view programatically but also allow the user to undo the operation - how do you do this?
----
Before you modify the text view, manually push the opposite operation onto the General/NSUndoManager stack. Write a small method along the lines of this:
    
- (void)undoableReplaceTextviewCharactersInRange:(General/NSRange)range withString:(General/NSString *)string
{
    General/NSRange inverseRange = General/NSMakeRange(range.location, [string length]);
    General/NSString *inverseString = [[textView string] substringWithRange:range];
    [[undoManager prepareWithInvocationTarget:self] undoableReplaceTextviewCharactersInRange:inverseRange withString:inverseString];
    [[textView textStorage] replaceCharactersInRange:range withString:string];
}

