---
layout: page
title: HowToPutASheetOnADocumentJustAfterOpeningIt
---



I'm trying to accomplish the following: a uses opens a new document in a document-based application. as soon as the document is loaded it has to show a sheet asking for some initial preferences. When I call the [NSApp beginSheet:modalForWIndow:ModalDelegate:didEndSelector:contextInfo] method in the windowControllerDidLoadNib method of the document, the sheet gets created, but is detached from the window. then the window is put in front of the sheet.

I suppose i have to call the beginSheet method somewhere between windowControllerDidLoadNib and the final presentation to the user, but where? which method in my document must I override to time the sheet correctly?

Any thoughts?

----

Hmm, a hackish way would be to use     performSelector:withObject:afterDelay: and maybe have a delay of a 1/10 of a second, then open the window after this delay.

*If you need it to work across the board you'd use an NSTimer instead.*

----

Wow, thanks for a lightning speed reaction, which also works like a charm. Reported this as a performance bug to the fruit company.

*Actually, a delay of 0 will work fine...this means that the selector will fire about as soon as the program idles. --JediKnil*

**Only if by "idle" you mean when the current cycle of the run loop finishes.** 

----

A less-hackish way is to implement an "app controller" class and instantiate it in your MainMenu.nib.

Implement the following action in this controller

    
- ( IBAction ) openNewDocument: ( id ) sender
{
	[ [ NSApplication sharedApplication ]
		sendAction: @selector( giveUsTheRealSheet ) to: nil from: self ];
}


Connect the File->New menu item to this action. The action is the one you use to display the sheet. I sheet you not.

*Ahh, I just sheet my pants.*      

**That was a *class* dump.**

----

Would this "openNewDocument" action also be called when the program is started from scratch, when an untitled document is opened?

*You could always implement something like the following -- also in the so-called app controller class*

    
- ( BOOL ) applicationShouldOpenUntitledFile: ( NSApplication * ) theApplication
{
	NSUserDefaults *defaults = [ NSUserDefaults standardUserDefaults ];
	
	if ( [ defaults boolForKey: NewDocStateKey ] )
		return YES;
	else
		return NO;
}

