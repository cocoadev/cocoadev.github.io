---
layout: page
title: MakingNSTextFieldActive
---


Hi, 

I've got an NSTextField that takes input from the user. Once the user types something and hits Enter, the textfield is not active anymore, ie. after hitting Enter, the user must click the textfield to make it active to type something else. How can I make the textfield active even after the user hits Enter. Thanks.
----
Similar question: I have a few NSTextFields which hold information until the user presses a "record info"-type button. I want to clear them after the button is pressed (done already) and put the keyboard focus back on the first field (I can't figure this out!). I have an outlet pointing to this NSTextField, and, based on the XCode docs, since an NSTextField derives from an NSControl, I thought I should be able to send a selectCell: message to self. However, this fails. Any suggestions? I'm sure I'm missing something simple, but then again, I'm still learning! Thanks! -dan

----

    myTextField window] makeFirstResponder:myTextField] should do what you want.
----
It should, but it doesn't. Here's what I have as code (theNameOutlet, of course, is an [[NSTextField from IB):     theNameOutlet window] makeFirstResponder:theNameOutlet]; I had tried this, plus every other method I could think of. For some reason, it's just not behaving the way the [[XCode documentation for NSTextField seems to suggest.... The keyboard focus stays on the last NSTextField that was active.

----

works here. are you sure the outlet is connected?
----
Well, I can pull the text from it in:     [theNameOutlet stringValue] and I can clear the text by     [theNameOutlet setString:@"some text"]. I'll play with it tomorrow, after a full restart of the system in case I managed to mangle some other part of the OS with the myriad of programming errors I've made in the past few days.
Nope. Restarted the Mac and it still fails to select the target NSTextField. There are several (four) in this window and it simply refuses to leave the last box entered despite the fact that the GDB shows it arrives at and executes the above command. I'm using 10.3.7.
Even more perplexing: Using GDB and the above code, I can show that the window thinks the first responder is, in fact, my particular NSTextField. If I break after the assignment and send a     [theNameOutlet window] firstResponder] delegate] message to GDB, it correctly returns the same object as     print-object theNameOutlet. However, in the program, the wrong text field still has the cursor and highlight ring:
    
(gdb) print-object [[[theNameOutlet window] firstResponder] delegate]
<[[NSTextField: 0x552600>
Current language:  auto; currently objective-c
(gdb) print-object theNameOutlet
<NSTextField: 0x552600>
(gdb) print-object theStartDateOutlet
<NSTextField: 0x550230>
(gdb) 

What's up with that? -dan

----

Huh? The text field's delegate is another NSTextField?? 

----
From what I've read, that's how it's supposed to work, bizarre as it may be. See: (XCode documentation) ADC Home > Reference Library > Documentation > Cocoa > Text & Fonts > Text Editing > "Working with the Field Editor," section How the Field Editor Works. It seems to suggest that the NSTextField is the delegate for the field editor. -dan

*This is 100% correct. The confusion comes from the fact that     [window firstResponder] is not the text field, but the FieldEditor.*

Unfortunately, even though I have read all this, I still can't seem to re-focus the keyboard's input on that NSTextField. Any suggestions? -dan

*Make a new test project that *only* does this, see if it works (it did for me) then figure out what you're doing differently in the real project.*

I forgot to mention that the window is actually a tabView. I don't know why that would be different, but perhaps there's a subtlety I'm not seeing. I'll try the "test project" idea.

*Yes, the tabview could certainly make a difference. Is the NSTabViewItem initialFirstResponder connected?*

Don't know the answer to that. I'm not sure how to do that. I did create a "test project" program and it definitely DOES work, so there's something else going on. Perhaps it is the NSTabView that's causing the trouble. Let me see if I can figure out something about that class. I used IB to create it but I don't really know how to access parts of it. If you have specific suggestions, they'd be most welcome! -dan

*I think you have to ctrl-drag from the tab title to set the item's outlets. I haven't done it in awhile.*

OK, I've got the programmatic interface responding to the NSTabView and I can pull the NSTabViewItem back from its identifier. I still can't force it to make that $*@#*@! NSTextField the active item, though. -d
This line works, (ie: without it, the tab doesn't put the keyboard focus on the NSTextField theNameOutlet, but with it, it's selected & the cursor is inside) if placed in awakeFromNib:

    theTabViewOutlet tabViewItemAtIndex:([theTabViewOutlet indexOfTabViewItemWithIdentifier:@"Requests"])] setInitialFirstResponder:theNameOutlet];

Any idea how to use that bit of info to make this work? -dan

Another odd observation: If I add an [[NSTextView to the window, I can send a     [myNSTextView window] makeFirstResponder:myNSTextView]; message. The insertion point can be seen in this view for a fraction of a second before it returns to the last NSTextField it was in. Somewhere, the cursor is being put back despite the apparently effective code to put it in this new, arbitrary NSTextView. It could be the same for the NSTextField. I don't know what might be forcing it back to the other NSTextField. It's not in my code, as far as I know. -d

Well, I'm out of ideas. It seems that NSWindow objects can pass control at will to various NSTextFields and NSTextViews, but NSTabViews cannot. Even when they try, they're overridden. Perhaps I have to subclass NSTextView, but I don't know which method is causing the redirection, so I'm not sure how to override it. I wonder why the behavior is so different between these two seemingly similar UI items.... -d
----
RESOLVED! Thanks to another CocoaDev page, of all things!!!! This is screwy, but here it is:
    
	theNameOutlet window] performSelector:@selector(makeFirstResponder:)
								 withObject:theNameOutlet
								 afterDelay:0.0];

It seems that you have to delay the selector for a brief moment to avoid the problem. The Cocoadev reference is: http://www.cocoadev.com/index.pl?[[FirstResponderSetAfterDelay. There's an elaborate description of the work involved in tracking this down, but that's enough for me right now. On to the next problem.... Thanks for all the help! This site is wonderful. -dan
----
Unfortunately, neither     [window makeFirstResponder: myTextField], nor 
    [window performSelector:@selector(makeFirstResponder:) withObject: myTextField afterDelay:0.0] (regardless of delay value) work for me when window I do     [NSApp runModalForWindow: window] or     [NSApp runModalSession: sessionForWindow]
any ideas? -- Artem

----

The delayed message sending works by scheduling the invocation in the run loop in NSDefaultRunLoopMode. So when the run loop's running in a different mode (like running modally), those invocations won't fire.

Check out the performSelector:withObject:afterDelay:modes: method, that lets you specify what modes the invocation will fire in. You'll need to pass in an NSArray that includes both NSDefaultRunLoopMode and NSModalPanelRunLoopMode to cause the invocations to fire when the run loop's running modally (see the documentation for NSRunLoop for more details).

Here's a code example:

    
NSArray *modes = [NSArray arrayWithObjects:NSDefaultRunLoopMode, NSModalPanelRunLoopMode, nil];
theNameOutlet window] performSelector:@selector(makeFirstResponder:) withObject:theNameOutlet 
        afterDelay:0.0 inModes:modes];


----
Yes, that helped, thanks. -- Artem
----
I'm currently fighting with this problem too.  And I've made an interesting discovery...

I have a subclass of [[NSTextField, and after noticing that resignFirstResponder: is called when I first click in the NSTextField to start editing, I decided to see if there isn't another view embedded in it which is taking focus.  Sure enough, I've found that there is a subview of type _NSKeyboardFocusClipView contained within the NSTextField.  So it would appear that the problem is due to the fact that you need to make the subview first responder, and not the NSTextField.

I'm going to give this code a try:

    
if (myTextField subviews] count]) {
     [[myTextField window] makeFirstResponder:[[myTextField subviews] objectAtIndex:0;
}
else {
     myTextField window] makeFirstResponder:myTextField];
}


and see what happens... nope, I give up.

----
That embedded view is called the [[FieldEditor.
----
It may not be what you're after, but in the past when I've wanted to make an NSTextField first responder, I've used selectText:
    
[myTextField selectText:nil];

----
I think my confusion is related to the fact that the NSTextField is the first responder (ie. it's getting keyboard events), but there is no cursor flashing in it.  Is it possible to get the cursor to show when it becomes first responder?
----
It turns out that the cursor was simply not visible because the text field was too small to show all of the text.  Using myTextField currentEditor] scrollRangeToVisible: ... ] solves the problem.  Now that I know about the [[FieldEditor, everything is making a lot more sense.  Apple should really mention the field editor prominently in the NSTextField documentation, because it's hidden away in the NSControl superclass, which is a counterintuitive place for it IMO.
----
The FieldEditor is used by many NSControl subclasses, such as NSComboBox and NSTableView, not just NSTextField. Talking about every important feature of every superclass in each class's documentation would bloat everything enormously.
----
Fair enough.  However, it's not even mentioned in the NSTextField companion guide.  So essentially, from the starting point of the NSTextField API document, there is nothing from there that would lead you to find it other than if you happened to stumble upon it in the NSControl API document.  Even just a link to the Text System Overview document from NSTextField would help.
----
If you have to "stumble upon" the documentation for the superclass of the class you're using, you're doing it wrong.

