---
layout: page
title: CrashWithModalWindows
---



I am having a problem using runModalForWindow: in my app. First off, let me 
 tell you what I have set up. 
  
 I have a Trial Information window that contains how many days are left in 
 the user's trial, etc. It is a modal window (NSWindow). One of the options 
 in the window is to register the program by entering the serial number. When 
 this option is chosen, another modal window is opened that allows the user 
 to this (also an NSWindow). The trial info window remains open. If the user 
 enters a correct name/number combination, a message box is displayed using 
 NSRunInformationalAlertPanel() letting them know that the program is now 
 registered. When they dismiss this panel, the modal loop for the register 
 window is stopped and the window is closed. The trial info window (when it 
 regains Key status) has code in it that will halt its own modal loop and 
 close if the program is registered. 
  
 This all works fine, except that, after the trial info window closes, 
 clicked anywhere in the menu bar causes the program to crash with either a 
 Signal 10 (SIGBUS) or a Signal 11 (SIGSEGV). I am completely and totally 
 clueless as to why this is happening. Could it have anything it do with 
 juggling two modal loops at the same time? Btw, here is the code I use to 
 end the modal loop for the trial info window in the delegate method: 
  
     
- (void)windowDidBecomeKey:(NSNotification *)aNotification 
 { 
 �� �if(aNotification object] isEqual: trialInfoWindow]) { 
 �� � � �// [self selectionChanged: mnuTrialSelection]; 
 �� � � �if([[[[RegistrationModel sharedInstance] isRegistered]) { 
 �� � � � � �[NSApp stopModal]; 
 �� � � � � �aNotification object] close]; 
 �� � � �} 
 �� �} 
 }

  
 The only thing I can think of as being the problem line is [[[NSApp stopModal] 
 since I am assuming that the modal loop for the trial info window is the 
 active modal loop (which seems logical). However, the program doesn't crash 
 while I am stepping through my own code in the debugger, only if I click on 
 one of the menu items in the menu bar after the trial info window closes. 
  
 Any ideas on this one? 
  
 -- MarcWeil
---- 

There's a chance that windowDidBecomeKey is being called more than once. Stick an NSLog(@"windowDidBecomeKey: %@", self) in the method and see what happens. 
----
It is being called more than once, but only for the register window, which isn't a problem because no action is taken if the windowDidBecomeKey: message is sent with the register window as [aNotification object].

Here is what is becoming key window, and when:

1. The trial info window

2. If the user chooses to enter the serial number, the registration window becomes key

3. A dialog will open telling the user if the serial they entered is valid or not, and when that closes, the registration regains key status.

4. If the serial is correct then the registration window is automatically closed, and the trial info window becomes key.

5. Then my windowDidBecomeKey: method goes through (it checks if the program is registered, and if it is, the modal loop for the trial info window is stopped and the window is closed, allowing for the rest of the application to continue starting up). This is, of course, where the program crashes (a SIGBUS signal is becoming the one that is more commonly happening now, instead of SIGSEGV).


Any more ideas on this one?

-- MarcWeil
----
A bit of an update on this problem. As I examine everything closely in the debugger, I find that, even after a call to [NSApp stopModal], the frontmost modal window (which is now closed) still remains the window that is returned by [NSApp modalWindow], which means that the second modal window (now the key window) is still in modal loop mode when it is closed.

Could this be the problem? It seems pretty suspicious to me.

-- MarcWeil

----

Try asking NSApp if a model window exists first before sending a "stopModal" message to NSApp. 

    

if ([NSApp modalWindow]) {
      blah, blah, blah
}



Also, I think it is not a good idea to stop a modal window in a method that received notifications. Why don't you stop the modal window in an action method like "OK" and "Cancel" (assuming you have "OK" and "Cancel" buttons).
----
I *do* ask NSApp if there is a modal window before doing anything to it. But it doesn't seem to matter because, even if there is a modal window and i do send stopModal to NSApp, NSApp still claims to have the *same* modal window (that's the key, as there should still be a modal window, just a different window) with the same memory address and everything (even though the window it claims to be modal has already received the close message).

Also, the only reason I have the stopModal code in a notification-receiving method is because the trial info window should close itself if the program becomes registered between the time that it lost key status and the time it regained it. I don't see any other way to impliment that behavior.

Thanks.

-- MarcWeil
----
Well, after about 1.5 hours of chatting with ZootBobbaLu in an iChat chatroom, we discovered that the problem to this seemigly evercomplex problem was quite simple. There were two main problems. The first is that Apple depreciated nested modal loops (and I was trying to make use of that). After discovering this, we tried to make the register window a sheet of the trial info window. But for some reason, that didn't work either. Figuring this out took up a good portion of the time (about an hour). We discovered that I was, stupidly, forgetting <code>[registerWindow orderOut:nil]</code>. It seems that we both forgot that it is necissary to hide the sheet before closing it. lol. Gotta love the life of programming!

Thanks again, Zoot!

-- MarcWeil

