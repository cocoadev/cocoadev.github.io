---
layout: page
title: HIDUtilitiesAndKeystrokes
---



I am trying to create an app that recognizes keystrokes across all applications. I am not convinced that the Carbon methods for this work in a Cocoa app, I have yet to see an example application where they do or read about anybody successfully doing this. And I've looked.

So it looks like the HID manager is my best bet. After running through all the available devices and isolating the current keyboard, I add all elements of the keyboard to the event queue using HIDQueueDevice(pRecDevice). I have a button in my Nib file that triggers an action which I want to display the keystroke events that have been queued up. Below is the method:

    
- (IBAction) checkQueue:(id)sender {
	IOHIDEventStruct event;
	while(HIDGetEvent(deviceCandidate, &event)) {
		if(event.type == kIOHIDElementTypeInput_Button) {
			switch(event.value) {
				case 1:
					NSLog(@"button pressed");
					break;
				case 0:
					NSLog(@"button released");
					break;
			}
			printf("\n MyPollDevice - Got Event: {t: 0x%2.2X, c:%ld, v: 0x%8.8lX}.",
				event.type,
				(UInt32) event.elementCookie,
				(event.longValueSize > 4) ? *(UInt32*)
				event.longValue : event.value);
		}
		
	}	
}


Below is what my run log looks like after pressing a single keystroke and releasing it:

    
2006-07-09 22:33:01.413 HIDtesting[16828] button released
 MyPollDevice - Got Event: {t: 0x02, c:11, v: 0x00000004}.
2006-07-09 22:33:01.413 HIDtesting[16828] button pressed
 MyPollDevice - Got Event: {t: 0x02, c:18, v: 0x00000000}.
2006-07-09 22:33:01.413 HIDtesting[16828] button released
 MyPollDevice - Got Event: {t: 0x02, c:21, v: 0x00000001}.
2006-07-09 22:33:01.413 HIDtesting[16828] button released
 MyPollDevice - Got Event: {t: 0x02, c:10, v: 0x04000000}.
2006-07-09 22:33:01.413 HIDtesting[16828] button released
 MyPollDevice - Got Event: {t: 0x02, c:11, v: 0x00000000}.
2006-07-09 22:33:01.413 HIDtesting[16828] button released
 MyPollDevice - Got Event: {t: 0x02, c:18, v: 0x00000000}.
2006-07-09 22:33:01.413 HIDtesting[16828] button released
 MyPollDevice - Got Event: {t: 0x02, c:21, v: 0x00000000}.


Why are there so many events queued up from a single button press? I would think there would be two at the most, one for keyDown one for keyUp. Any ideas? Thanks - Charlie

----

http://lists.apple.com/archives/Quartz-dev/2005/Oct/msg00013.html

----

This looks interesting, thanks! I need to incorporate gamepad support into my app as well, so I'd like to continue on with my quest to use the HIDUtilities, but other people interested in receiving keystroke notifications  should definitely check out the above link. The other problem is that it's Tiger only if I read correctly... 

----

Okay, it looks like the longValue property of the IOHIDEventStruct structure tells you whether a key was pressed or released and also gives you the value of the key with a tiny bit of finessing. By changing the function given above to the following I get the results that I was looking for; namely that I'm told which keys have been pressed and which keys have been released without any extra events thrown in (still not sure what those extra events were...).

    
- (IBAction) checkQueue:(id)sender {
  IOHIDEventStruct event;
  while(HIDGetEvent(deviceCandidate, &event)) {
    if(event.type == kIOHIDElementTypeInput_Button) {
      if(event.longValueSize > 4 && *(UInt32*)event.longValue > 1) {
        NSLog(@"key released, the value is %i", (*(UInt32*)event.longValue)>>24);
      }else{
        if(event.value > 3) {
          NSLog(@"key pressed, the value is %i", event.value);
        }
      }
    }
  }		
}


This generates the following output after three keypresses / releases"

    
2006-07-10 00:45:25.186 HIDtesting[17309] key pressed, the value is 4
2006-07-10 00:45:25.186 HIDtesting[17309] key released, the value is 4
2006-07-10 00:45:25.186 HIDtesting[17309] key pressed, the value is 22
2006-07-10 00:45:25.186 HIDtesting[17309] key released, the value is 22
2006-07-10 00:45:25.186 HIDtesting[17309] key pressed, the value is 9
2006-07-10 00:45:25.186 HIDtesting[17309] key released, the value is 9


The values correspond with the constants defined in IOHIDUsageTables.h for the KeyboardOrKeypad Page.

Sweet.

----

After leaving this project for a while and coming back to it I've realized that both the key pressed and key released messages are created when the key is pressed. No message is created when the key is released. Any ideas on how to correctly determine when a key is released are GREATLY appreciated. - Charlie

----

OK, after much hand wringing I finally got this to work. The basic procedure I wound up using is as follows:

*1. Queue up your device and assign a callback function to it for whenever an event is placed in the queue.
*2. In your callback function, check the elementCookie of each event structure that is placed in the queue. If it's less than 10, a modifier has been pressed or released. If it's over 20, it's one of the other keys (can't remember what 11-19 do right now...)
*3. When you find an event with one of these cookies, check the events value. If it's not equal to 0 then it's a press, if it's 0 then its  a release.
*4. If it's a press, store the cookie in to an array of that will hold cookies corresponding to all the keys that have been pressed.
*5. If it's a release, look at each cookie in your array. Loop through all the elements in your device. Once you find the element that matches the cookie stored in the array, check it's value. If it's 0 you know that this cookie in the array shows the element that was released. The others are still being held.

It seems to be working pretty well for me so far... - Charlie

----
Well hurray, now we can all write keystroke loggers - I hope Apple fixes the security of this API!
----
Could you please tell me includes,frameworks to make your code workable? i tried your code, gives so many errors. What is 'devideCandidate' in above code? 

----
The Carbon version works fine from within Cocoa.
You may find this useful:

http://dbachrach.com/blog/2005/11/program-global-hotkeys-in-cocoa-easily/

Code sample for the impatient:

    
#import "KKHotKey.h"
#import <Carbon/Carbon.h>

void * referenceToDocument;
OSStatus MyHotKeyHandler(EventHandlerCallRef nextHandler,EventRef theEvent,
void *userData)
{
        NSLog(@"Keypress\n");
        [(id)referenceToDocument clickThrough:nil]; //callback on main thread to do something useful when the hotkey is pressed
        return noErr;
}

@implementation KKHotKey
- (void) keyPressEntry:(id)arg
{
        referenceToDocument = arg;

        //Register the Hotkeys
        EventHotKeyRef gMyHotKeyRef;
        EventHotKeyID gMyHotKeyID;
        EventTypeSpec eventType;
        eventType.eventClass=kEventClassKeyboard;
        eventType.eventKind=kEventHotKeyPressed;

        InstallApplicationEventHandler(&MyHotKeyHandler,1,&eventType,NULL,NULL);

        gMyHotKeyID.signature='htk1';
        gMyHotKeyID.id=1;

        RegisterEventHotKey(49, controlKey+optionKey, gMyHotKeyID,
        GetApplicationEventTarget(), 0, &gMyHotKeyRef);  // control + alt + space

        RunApplicationEventLoop();
}
@end



And from the main thread:
    
KKHotKey * hotkey = [[[KKHotKey alloc] init] retain];
[NSThread detachNewThreadSelector:@selector(keyPressEntry:) toTarget:hotkey withObject:self];


Hope this helps,
--CristianDraghici
----
I just read your link.actually i need some thing else. recently i saw above HIDGetEvent() code, i think it works fine though without 'Enabling Access for  Assistive devices'. but eventTap will not work without that. so i just wanted to know how to go about HIDGetEvent()... 

----
Doing     [[[SomeClass alloc] init] retain] is basically always an error. See MemoryManagement.
----
Can any one please tell me how to use HIDGetEvent method? What are its includes, frameworks?

----

You implement HIDGetEvent yourself, it's not a API function.

http://developer.apple.com/samplecode/HID_Utilities_Source/listing9.html

--CristianDraghici

