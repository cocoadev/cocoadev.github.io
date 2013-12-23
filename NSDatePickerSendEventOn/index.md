---
layout: page
title: NSDatePickerSendEventOn
---



I have a little config sheet that I prompt the user with.
It includes an NSTextField and an NSDatePicker.
I want the user to be able to hit enter at any time while editing either cell, and have the sheet automatically close.

This is easy with NSTextField.  I can simply set configure it in Interface Builder to "Send action on" -> "Enter only".  Simple.

But I can't figure out how to get this functionality with NSDatePicker.
If I simply connect it to a method, it fires every time the user types a number.

Can I configure the NSDatePicker to only fire on Enter?
Or can I find out what the last key the user hit was?

----
----
You could check the last event sent and then continue with the method if it's the correct key.  I've put some code below (untested) that gets the current event and does basic checks for newline and carriage return characters (which should indicate the return key was pressed, I think).  If it doesn't get either, it quits executing the code. -G

    
-(IBAction)datePickerAction:(id)sender
{
    NSString *characters = [[NSApp currentEvent] characters];
    if (!characters || ![characters length] || [characters characterAtIndex:0] != '\r' || [characters characterAtIndex:0] != '\n') return;
    /* Whatever else you want to do */
}


----
Excellent suggestion!
I tried it out with the following code.  (Just checked the keyCode instead of the characters)

    
-(IBAction)datePickerAction:(id)sender
{
    unsigned int keyCode = [[NSApp currentEvent] keyCode];
    if(keyCode != 36 && keyCode != 76)
    {
        // User didn't hit enter or return: Ignore message
        return;
    }
    /* Whatever else you want to do */
}


This properly ignores messages sent when the user is typing in numbers.
There's only one problem now:
NSDatePicker doesn't send the message on Enter or Return!  Argh!
It only seems to send the message when the user enters numbers, which change the date/time.

Anybody have any ideas?

----

It might be cleaner to use a custom transparent container view for both views that has \r for a key equivalent (-[NSResponder performKeyEquivalent:]).  However, at that point it starts to look pretty suspicious that there is no visual communication to the user.. have you considered putting a default button in the sheet?  

----

*Indeed. Requiring the user to press return to get his changes to stick without an obvious visual indication that this is required (such as a big pulsing OK button) is not a good way to do things. It's not how things are generally done on the platform, and so users are just going to get confused as to why their changes haven't taken effect, rather than realizing they have to press enter to make it happen. (This has happened to me before, so I'm not just being hypothetical.)*

----

----
have you considered putting a default button in the sheet?
----
Yup.  There is a default 'OK' button on the sheet.  (So I make sure to have a visual indication)  And my first idea was exactly what you suggested.  I set the key equivalent of the button to be 'Return' so I got the big pulsing OK button.  But It didn't work when I tested it.  However, upon further inspection, I've found something interesting.  When setup this way, with the OK button using a key equivalent of 'Return' and nothing else... (that is, the NSTextField and NSDatePicker are not connected to anything) hitting Return while in the NSTextField works perfectly (just as expected), but hitting Return while in the NSDatePicker does nothing at all.  HOWEVER, hitting Enter while in the NSDatePicker does work!  (Yes, there is a difference.  The enter key is the one by the number pad.  Or if you're using a mac laptop, the enter key is accomplished by hitting Fn+Return)

I'm not really sure what to think about this.  Is it an internal bug?  Is it supposed to be this way?  Is there a workaround for this?

----

----
Sounds buggy to me. Can you produce a small test case that can exhibit it? That way we could see if there might be an error in it, and if not then you can attach it to your bug report to Apple.

As a workaround, you might try overriding     performKeyEquivalent: in the sheet (you'll have to subclass NSWindow or NSPanel for this) to catch the keypress. And if *that* doesn't work, you can always override     sendEvent:, although this is an action of last resort.

----

----
Can you produce a small test case that can exhibit it? 
----

Excellent suggestion.  I just whipped one up.
I created a new Cocoa application in Xcode, edited MainMenu.nib.  Added an NSTextField, NSDatePicker, and NSButton to the default window.  Then the only thing I did was set the keyEquivalent of the button to be 'Return', and connected the button to an action in the AppController.  The only code I wrote was an NSLog to output everytime the button was firing.  Here it is:

    
#import "AppController.h"

@implementation AppController

- (IBAction)myAction:(id)sender
{
	NSLog(@"myAction called");
}

@end


I get the exact same functionality I did when using the sheet.  Hitting 'Return' while in the NSTextField has the desired effect of pushing the OK button.  Hitting 'Return' while in the NSDatePicker does nothing.  However, hitting 'Enter' while in the NSDatePicker has the effect of pushing the OK button.  What's more, it pushes the OK button TWICE.  Once on keyDown, and once on keyUp!

I'll look around some more and see if this is a know bug.  I'll also try your suggestions for overriding various methods.
Is it possible to set the button's keyEquivalent to be 'Enter' instead of 'Return'?  I know I can't do it in Interface Builder.  But maybe I could do it in code??

----

----
Sounds like a bug to me. I would encourage you to file it even if you discover that it's a known bug. Each bug report puts more weight behind the bug and increases the chances and speed of a fix. And with a fairly clear chain of events and a test case you can attach to it, you'll hopefully get some kind of response (although don't count on it).

As far as setting Enter instead of Return, I don't think you can. In any case, the two are supposed to be (and usually are) equivalent in this case. But if you want to try it anyway, the method you're looking for would probably be NSButton's     setKeyEquivalent:.

----

I just submitted a bug report to Apple, and included the small test case I whipped up earlier.  I'll try to report back here if they respond, or classify it as a duplicate bug, etc.  In the meantime I'll be testing the various workarounds suggested here. [did Apple ever report back? This is still biting me on 10.4.8.]

