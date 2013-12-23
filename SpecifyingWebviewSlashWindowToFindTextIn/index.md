---
layout: page
title: SpecifyingWebviewSlashWindowToFindTextIn
---



I've been working on a broweser, and since WebViews don't support the default find panel, I've had to construct my own. Not difficult at all - a little annoying hooking everything up and all - but I've run into a small problem. My findNext: action works as expected if the user uses the Find Next menu option, but not if he clicks the Find Next button in the Find panel. Instead, it tries to find a webView in the find panel to search, and of course, fails. Is there some way I can specify for it to search the frontmost window WITH a webView?

Here's my current code if it's needed:

    
- (IBAction)findNext:(id)sender
{
	NSUserDefaults *defaults;
	defaults = [NSUserDefaults standardUserDefaults];
	
	[webView searchFor:[findTextField stringValue] direction: YES caseSensitive: [defaults boolForKey:@"findCaseSensitive"] wrap: [defaults boolForKey:@"findWrap"]];

}


Thanks in advance for any help!

----

Anybody at all have an idea/suggestion?

----

fwiw I filed a request for WebView to support the standard find panel, and someone has offered a 100 euro bounty to anyone that implements it: http://bugzilla.opendarwin.org/show_bug.cgi?id=3640 --DavidSmith

