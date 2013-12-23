---
layout: page
title: AppleScriptSupportLogging
---

I want to learn how to support Applescript... but even the basic ones don't seem to be communicating how to do it to me... so, here's what I want (for starters) is a cocoa app that only responds to a single applescript command, that only takes a string...like say log...

0) new project -> cocoa application -> MyASTest

1) set info.plist key "NSAppleScriptEnabled" to @"YES"

2) add a file to the project MyASTest.scriptSuite (a plist), and edit it like so 

    
 {
 	Name = MyASTestSuite;
 	AppleEventCode = MATs;
 	Classes = {
 		MyASTest = {
 			Superclass = "NSCoreSuite.NSApplication";
 			AppleEventCode = capp;
 			SupportedCommands = {
 				MyASTestSuite.Log = "log:";
 			};
 		};
 	};
 	Commands = {
 		Log = {
 			AppleEventCode = MAlo;
 			CommandClass = NSScriptCommand;
 			Type = ""; /*return type*/
 			Arguments = {
 				Target = {
 					AppleEventCode = MAlt;
 					Type = NSString;
 					Optional = NO;
 				};
 			};
 		};
 	};
 }


3) put a category on NSApplication to handle it

    
 @interface NSApplication (LogScript)
 - (id)log:(NSScriptCommand *)command;
 @end
 
 @implementation NSApplication (LogScript)
 - (id)log:(NSScriptCommand *)command
 {
 	NSLog(@"%@",command evaluatedArguments] objectForKey:@"target"]);
 	// not quite sure about that line... but I never get here to test it
 	return nil;
 }
 @end


4) include category in main.m

5) test it with a short applescript

    
 tell application "MyASTest" to log "hello, applescript!"


... and that's what I've got... it doesn't work... It currently does nothing...(well the main window shows up, and it doesn't give me parse errors for the plist anymore...)

----

I think the problem is that you are using a string as a direct parameter. [[CocoaScripting is probably trying to send the "log" event to NSString. See DirectParametersAsValues for more information, but a quick fix would be to add a named parameter for the text. --DustinVoss

----

Is that it do you think?  I dunno... If it was the direct parameter thing wouldn't it still get into the block?  or at least pump out some kind of error?  Just I don't get any action at all so it's very tough to figure it out... well I'll try naming the log's parameter and report back in a bit...

I turned on NSScriptDebugging... I saw that it wasn't loading the suite, so I gave the suite a real apple event code (the plist above reflects my changes) and now I get the following error:

    
 2004-07-29 08:39:59.210 MyASTest[11424] argument terminology dictionary not found for name=Target
 2004-07-29 08:39:59.227 MyASTest[11424] No readable name in dictionary


Do I need to separately declare information for each argument?  I haven't seen that in other dictionaries... one more question... if I don't use the direct parameter route, how do I use the applescript? *tell application "MyASTest" to log message "hello applescript!"* ???

----

Do you have a .scriptTerminology file with an entry for the "Target" argument? (I use .sdef files, myself.)

----

just made one with the same results... here's the scriptTerminology file (most basic version)... I should also say that the error occurs in trying to open the dictionary from Script Editor... just running     tell application "MyASTest" to log "hello applescript" does nothing and if I try to put the word message between the words log and hello, the applescript doesn't compile...

    
 {
 	Name = "MyASTestSuite";
 	Classes = {
 		NSApplication = {
 			Name = MyASTest;
 			PluralName = MyASTest;
 		};
 	};
 	Commands = {
 		Name = Log;
 		Arguments = {
 			Message = {
 				Name = "Message";
 			};
 		};
 	};
 }

----
Well, if the parameter is called Message, then the .scriptSuite has to say *Message = {...}* rather than *Target = {...}*. But really, I think it would be easiest to use an sdef file.

well... I'll try that... but I think I came up with another problem... applescript has a built-in log command... but I still need to figure this out so I will be changing that name too...

