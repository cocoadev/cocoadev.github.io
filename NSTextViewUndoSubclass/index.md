---
layout: page
title: NSTextViewUndoSubclass
---



I'm playing with undo support with my NSTextView, and trying to make it as painless as I can for myself.  So I subclassed NSTextView and came up with this:

    

- (void)replaceCharactersInRange:(NSRange)range withRTFD:(NSData *)rtfdData; {
    
    NSData *oldData = self textStorage] [[RTFDFromRange:range documentAttributes:nil];
    
    NSUndoManager *undoManager = self window] undoManager];
    
    [[undoManager prepareWithInvocationTarget:self]
        replaceCharactersInRange:range withRTFD:oldData];
    
    [super replaceCharactersInRange:range withRTFD:rtfdData];
}



It seems to work just fine.. but it makes me uncomfortable for some reason.  Is what I'm doing insane?  Or is this perfectly legal?

----

It looks sane to me. You should probably modify the range you pass to the undoManager; the range you need to replace in order to undo the action won't be identical to the range you receive, as the length could be different. The only other concern is whether this method actually catches all modifications or not. If it does, great.

----

In general, I found that it is sufficient to wrap a set of changes to an [[NSTextView's textStorage inside a group of shouldChangeTextInRange:replacementString: and didChangeText calls. Once you do this, NSTextView takes care of registering the correct undo notifications itself. This works perfectly under 10.4 (don't know about earlier versions), but it doesn't seem to be documented anywhere.

**Undo in NSTextView has been around since at least 10.2, and likely even since the original NS project. Your method seems pretty weird, but if it allows undo of programmatic edits, go for it. Here's some Apple documentation on the subject under NSTextView in Using Undo in Applications in the Undo Architecture section. --JediKnil**

*NSTextViews provide undo and redo behavior by default. For your application to take advantage of this feature, however, it must send setAllowsUndo: with an argument of YES to the text view. If you want a text view to have its own NSUndoManager (and not the window’s), have the text view’s delegate implement undoManagerForTextView:, to return the NSUndoManager.

The default undo and redo behavior applies to text fields and text in cells as long as the field or cell is the first responder (that is, the focus of keyboard actions). Once the insertion point leaves the field or cell, prior operations cannot be undone.*

