---
layout: page
title: NSTextField
---

AppKit

NSTextField : NSControl : NSView : NSResponder : NSObject

http://developer.apple.com/library/mac/#documentation/Cocoa/Reference/ApplicationKit/Classes/NSTextField_Class/Reference/Reference.html

Below are some relevant CocoaDev articles. You can list articles here by including \\%\\%BEGINENTRY\\%\\%NSTextField\\%\\%ENDENTRY\\%\\% somewhere in the article.

[Topic]

To handle start and end of editing session, use one of these delegate methods:

    
-controlTextDidBeginEditing:
-controlTextDidEndEditing:


To control a gain or loss of "focus" in your text-field, use one of these methods:

    
   -(BOOL)becomeFirstResponder
   -(BOOL)resignFirstResponder


You can tell which NSTextField's value changed from inside a     -controlTextDidChange: message by getting the delegate of the field editor and comparing its pointer value with the pointer values for your NSTextField instances. For example:

    
   - (void)controlTextDidChange:(NSNotification *)nd
   {
      NSTextField *ed = [nd object];
      
      if (ed == hostName) [self hostNameChanged];
      if (ed == displayName) [self displayNameChanged];
   };


Detect keyDown events in a delegate of NSTextView:
    
   - (BOOL)control: (NSControl *)control textView:(NSTextView *)textView doCommandBySelector: (SEL)commandSelector 
   {
      NSLog(@"entered control area = %@",NSStringFromSelector(commandSelector));
      return YES;
   }


----
Interesting tech note on recognizing tab and enter in NSTextField:
http://developer.apple.com/qa/qa2006/qa1454.html

----
I'm subclassing NSTextField so that I can invoke my own code in the becomeFirstResponder method.  So far so good, but then I discovered that the code I put in that method does not know about my app's controller instance.  If I could just send any message at all to my controller instance, like this:

    
   - (bool) becomeFirstResponder 
   {
       bool  superResult=[super becomeFirstResponder] ;
       [myController firstResponderChanged];
       return superResult; 
   }


Then I could do whatever I wanted in my controller's firstResponderChanged method.

However, in the above code example, "myController" is fictional, and despite searching high and low with Google and Apple's own Cocoa reference materials, I can't figure out how to refer to my controller instance in code.  (In my controller's own methods, I can use "self", but that doesn't help in this case.)  Interface Builder shows my controller class instance as an icon, but doesn't seem to have any instance name for it -- just its class name.  Its info box (Attributes, Connections, etc.) also didn't help.

Any ideas?

Much thanks --DarelRex@gmail.com

----

If your using bindings you should be able to just do this:

    
   id controller = self infoForBinding:contentAttributeKey] objectForKey:@"[[NSObservedObject"];


If not, then you could add a controller ivar to your subclass with appropriate getter/setter methods, but I'm not really sure thats the best idea.

-- j

----
Or just add an IBOutlet. It's the standard thing to do and it works just fine.

On another note,     bool is not the same as     BOOL and if the system expects you to return     BOOL then you should do that, as there is no guarantee that the calling conventions will be compatible.

----
Thanks for the valuable info, all.  I am changing that to BOOL (hopefully my compiler warning will then go away!).

Researching bindings and outlets, it suddenly occurred to me that since my controller is already hooked up as the delegate of my subclassed text fields, I could just do this:

    
   [_delegate firstResponderChanged];


It works!   :)  --DarelRex@gmail.com

----
You'll note this comment in the interface of NSTextField:     /*All instance variables are private*/. So while it's legal to access the     _delegate ivar directly, it's much better to use     [self delegate] instead.

----
Thanks for the tip -- especially since _delegate refuses to compile when I try to use it in a subclass of NSTextView.  I will use [self delegate] instead.  --DarelRex@gmail.com

----
I want the user to be able to drag-and-drop a file onto an NSTextField and have the file's path displayed in the NSTextField. This works for the most part since drag-and-drop is provided for free. However, when the user drags-and-drops a file onto an NSTextField that already contains text, the path is just inserted into the text. I'd like the text of the NSTextField to be replaced. Any idea on how to do that? Thanks!

----
Q. I read in NSTextField class reference that it implements     textDidBeginEditing and     textDidEndEditing delegate methods, but they don't seem to work (at least for me). Am i doing something wrong? I declare them in my controller's header and implement them in implementation file. Should i use NSControl delegate methods instead? I'm a bit confused -- EimantasVaiciunas

----
A. You should use 

    
   - (void)controlTextDidChange:(NSNotification *)aNotification
, 

    
   - (void)controlTextDidBeginEditing:(NSNotification *)aNotification
, and 

    
   - (void)controlTextDidEndEditing:(NSNotification *)aNotification
. 

Yes, the documentation for the delegate methods is in NSControl. -- AralBalkan

