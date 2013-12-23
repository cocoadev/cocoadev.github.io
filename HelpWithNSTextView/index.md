---
layout: page
title: HelpWithNSTextView
---

Here's my situation:  I have an NSTextView in a window that I want to display data for the user, but at the same time have the NSTextView uneditable.  
I had been using -insertText, but discovered that I cannot when I have the NSTextView set to be uneditable.  Short of surrounding all my inserts with [myTextView setEditable:YES];
and
[myTextView setEditable:NO];
what else can I do?
 
I created the NSTextView in Interface Builder, created an outlet to receive that data.  I then have the outlet declared in the header file, and then using it in the class file.  I really, really would like to keep this limited to my one class file (it's a simple app, mostly for learning) and not create any more if necessary.  That all being said, what can I do? :)

----
There's a couple of options here

*Get the text storage with     [myTextView textStorage] and make the changes directly to that; it's a subclass of NSMutableAttributedString, so all methods of that class work on it (specifically     -insertAttributedString:atIndex:).  Also, take a look at the     -mutableString method if you want to make changes without worrying about attributes (it'd look something like     [myTextView textStorage] mutableString] insertString:aString atIndex:anIndex]; in that case).  
*[[NSTextView also inherits the     -replaceCharactersInRange:withString: from NSText; if you pass in a range with a length of 0, it's functionally identical to inserting text.
*There's also the -setString: method inherited that NSTextView inherits from NSText for utter simplicity; it just replaces all the text in your text view with the string you pass. This isn't quite the same as inserting but may be all you need.
 
Best of luck.  -- Bo
----
I have no idea where to start with 

*    -(void)textDidChange;

Can anyone help?

----

Just make your class the NSTextView's delegate (in IB or via*     [textView setDelegate:myClassInstance]*) and implement the

    -(void)textDidChange:(NSNotification *)aNotification method in it. That method will be called whenever the text view's text changes.

----

I want to change an NSTextView's font color when the user clicks the NSTextView, thus editing the text inside.  Which delegate method do I use to do this? I tried:

    - (void)textView:(NSTextView *)aTextView
   clickedOnCell:(id <NSTextAttachmentCell>)cell
              inRect:(NSRect)cellFrame
            atIndex:(unsigned)charIndex

but it didn't work. I'm also trying to do the same thing with a NSTextField...

----

You could try     textViewDidChangeSelection:, but what it sounds like you really want to do is watch when the window's FirstResponder changes.

----

OK in my text view I have some HTML links that I parse out using a modified version of the example code at ClickableUrlInTextView. So when I'm switching from a file to another (using a table view), sometimes all of the text in the text view becomes one big link. I'm not sure why this is happening, because most of the time it correctly shows all the links separate from the other text. I use setString: and set the text view to an empty string before adding attributes, but is there another way to reset it? Thanks, --KevinWojniak

Ok nevermind I solved it (I think) with this:
    [textView textStorage] fixAttributesInRange:NSMakeRange(0, textView string] length])];

----
Is this not alot easier and efficient?:
    [resultTextView replaceCharactersInRange:[[NSMakeRange(resultTextView string] length],0) withString:[[[NSString stringWithFormat:@">%@\n",[sender stringValue]]];

----

I used     replaceCharactersinRange:withRTF: to write an attributed string into my uneditable text view. Having to extract the RTF from the attributed string was a bit of a fag though. Is there an easier way to do it? *Ummmm ... just to disambiguate, do you mean RTF from a string is homosexual, a cigarette, or a bundle of sticks? :-P -- Ah, never mind. It's a British thing. A bit of a *chore*, I guess. <laughs at self> See this from an American perspective (if you dare). ;-)*

What I'd really like to do though, is to have a TextView that  acts like XCode's logging window, with logging messages appear in red (I've got that  bit working), that accepts user input, but always appends it to the end of the textview, in black. I've looked at all the docs, but its still not obvious to me where to start ...

