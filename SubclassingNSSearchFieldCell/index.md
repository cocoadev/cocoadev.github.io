---
layout: page
title: SubclassingNSSearchFieldCell
---

I have an General/NSSearchField in an General/NSToolbar and a large General/WebView spanning the content of the parent window. I want the General/NSSearchField to behave like the search field in the Dictionary app included with Tiger, which passes up/down arrow and pg up/down key presses to the General/WebView to let the user scroll without leaving the search field.

I've tried subclassing General/NSSearchFieldCell and overriding its keyDown:, but calling [searchField setCell:newCell] breaks the General/NSSearchField so that only the magnifying glass is displayed.

I've also tried subclassing just General/NSSearchField, but its keyDown: never gets called.

Am I going about this the wrong way? I'd be grateful for any help.

-General/JustinAnderson

----

Read up on the General/FieldEditor, and add this to your search field's delegate:

    
- (BOOL)control:(General/NSControl *)control textView:(General/NSTextView *)textView doCommandBySelector:(SEL) commandSelector
{
     BOOL retval = NO;
     if (commandSelector == @selector(moveDown:)) {
         retval = YES;
         // manipulate your web view
         [theWebView scrollLineDown:nil];
     } else if (commandSelector == @selector(moveUp:)) {
         retval = YES;
         // manipulate your web view
         [theWebView scrollLineUp:nil];
     }
     return retval;
}


The General/FieldEditor is the first responder, and it receives the original keyDown: message.  However, it has plenty of delegate methods, so there is no reason to subclass to change this behavior.

----

Thank you very much! I edited the above code to include the commands that should be passed to the General/WebView.
- General/JustinAnderson
