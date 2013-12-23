---
layout: page
title: NSAlertSheetProblemsWithOtherSheets
---

Hi,

I have an app which uses an NSAlert as a sheet, which I understand is quite a standard Cocoa thing., here is the code...

    
NSAlert *alert = [[[NSAlert alloc] init] autorelease];
			[alert addButtonWithTitle:@"Switch"];
			[alert addButtonWithTitle:@"Cancel"];
			[alert setMessageText:@"Switch tasks?"];
			[alert setInformativeText:@"You are currently working on another project, switching projects will end that work session, are you sure you want to switch?"];
			[alert setAlertStyle:NSWarningAlertStyle];
			
			[alert beginSheetModalForWindow:window modalDelegate:self didEndSelector:@selector(alertDidEnd:returnCode:contextInfo:) contextInfo:nil];


This displays the alert just fine, no problems here at all, when the user dismisses the alert with their answer ('yes switch' or 'cancel') the following method is called (by the 'didEndSelector' seen above):

    
- (void)alertDidEnd:(NSAlert *)alert returnCode:(int)returnCode contextInfo:(void *)contextInfo 
{
	//an alert has ended...
	if (returnCode == NSAlertFirstButtonReturn) 
		{ 
		[taskWindowController doSomething];
		}
}


As you can see, this method (called supposedly when the alert ends) calls another method in turn if the user selected the main button. No problems yet... until [taskWindowController doSomething] is called, because you see, this method opens a sheet of its own. I didn't think this would be a problem, because I assumed that by the time the 'alertDidEnd' was reached the original sheet... er... would have, well, ended?

Unfortunately it turns out not to be the case, and what happens is, the second sheet appears behind the original alertSheet, then the alertSheet disappears, and THEN my entire app window disappears, leaving the second sheet floating in the middle of the screen on its own.

Strange.

I has really assumed alertDidEnd would be called when the alertSheet was all over, leaving the way free for another sheet to appear... but it doesn't seem to.

How on earth am I going to figure this little problem out? Any help would be much appreciated!

Thankyou for your suggestions!

----

You need to send your sheets an     orderOut: message to get them to go away. See the section on multiple sheets at [http://developer.apple.com/documentation/Cocoa/Conceptual/Sheets/index.html]

