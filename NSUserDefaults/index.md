---
layout: page
title: NSUserDefaults
---

I am looking for an example in which NSUserDefaults has been used. Can anyone help out?

----
http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/

----
NSUserDefaults is a Cocoa Foundation class that allows an application to easily get and set its own preferences. It allows each user on the computer or network to have their own preferences by storing the preference file at:     ~/Library/Preferences/the.bundle.identifier.plist

WorkingWithUserDefaults is the spiffy CocoaDev introduction to the subject.

For a great Easy intro into how to use the defaults system see MikeBeam's article here: http://www.macdevcenter.com/pub/a/mac/2001/08/24/cocoa.html  (Go to the third page)

*
That's one of Mike's early articles though, so read a bit skeptically.  I note that on the first page he states that     [NSString stringWithString:@"aString"] is better than just     @"aString" because the former will use less memory.  Mmm, nope.
* 

----
One can use the     defaults command line program to access the defaults database.
This can be useful when writing shell scripts.  

----
You're probably here to see an example....

    
 -(void)saveToUserDefaults:(NSString*)myString
 {
 	NSUserDefaults *standardUserDefaults = [NSUserDefaults standardUserDefaults];
 
 	if (standardUserDefaults) {
 		[standardUserDefaults setObject:myString forKey:@"Prefs"];
 		[standardUserDefaults synchronize];
 	}
 }
 
 -(NSString*)retrieveFromUserDefaults
 {
 	NSUserDefaults *standardUserDefaults = [NSUserDefaults standardUserDefaults];
 	NSString *val = nil;
 	
 	if (standardUserDefaults) 
 		val = [standardUserDefaults objectForKey:@"Prefs"];
 	
 	return val;
 }


----
A few tidbits:

NSGlobalDomain is stored at     ~/Library/Preferences/.GlobalPreferences.plist

if you find that defaults are not being saved all the time, try asking the standard default object to reset     [NSUserDefaults resetStandardUserDefaults]; before the application quits

If NSUserDefaults is not flexible enough for you, try the CFPreferences API (which is another interface to the same defaults system).  This is covered on WorkingWithUserDefaults.

----
I read in WorkingWithUserDefaults that network domains are not supported (as of this writing) -- anyone has the scoop on this?

I really need defaults to be taken from     /Network/Library/Preferences as well. It's probably straightforward to just read the plist myself, but I wonder, why isn't this supported? and can it somehow be enabled?

*Whatever you do, don't read the plist directly. The fact that user defaults are stored in plists is an implementation detail, and it's not guaranteed to be like that forever. Usually, when NSUserDefaults is too restrictive, you can switch to CFPreferences to get more capabilities and flexibility. With the magic of TollFreeBridging, it's relatively painless, too.*

Yes, but CFPreferences also ignore     /Network/Library, so I don't really have a choice.

*In that case, you are doomed! Sorry, I didn't know about that.*

----
when i use objectForKey i am getting a number returned.  i know it is not a number i checked the preference file manually to see.
i set up my preference as a NSString.  is there something i am doing wrong? this is the code i am using to call the preference

    
	NSString *interfacePref = nil; 
	interfacePref = NSUserDefaults standardUserDefaults] objectForKey:FDXInterface ];  // @"interface"
	[self sendData: [NSString stringWithFormat: @"set interface %d" "\n", interfacePref, [sender tag];


Not sure this is the problem, but something wrong in the third line of code. You only have %d, which will be applied to interfacePref, and will be a number of course, because %d is for integer. So the interfacePref address (interfacePref is an id = a pointer) will be displayed as an integer. You should use %@ somewhere in your format string.

that was it. i totally over looked that...  thanks!

----
CPPersistentDefaults can be used to easily add NSUserDefaults-like behavior to Core Data documents.  NSUserDefaults for application-wide preferences, and CPPersistentDefaults for document-specific preferences.

----
Here's a tutorial that will show you how to add a preferences window to your application, and wire it up to NSUserDefaults using CocoaBindings. It also shows you how to set and use these defaults from your code.

http://www.mere-mortal-software.com/blog/details.php?d=2007-03-14 (Dead link)

----
Are NSUserDefaults cached?  Should I not query the same default too often and instead cache the result in my own var?

