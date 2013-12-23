---
layout: page
title: NSOpenPanel
---

from: http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOpenPanel_Class/index.html

NSOpenPanel provides the Open panel for the Cocoa user interface. Applications use the Open panel as a convenient way to query the user for the name of a file to open. The Open panel can only be run modally.

----

The Apple docs on this only show you directly how to throw an open panel (or, indeed, an NSSavePanel) up as a separate, window.  If you want to use sheets you have to do a little more work.  But only a little.

.h file :
    
...
// the method that shows the sheet
- (void) someMethod;

// handler for sheet ending
-(void)filePanelDidEnd:(NSWindow*)sheet
            returnCode:(int)returnCode
           contextInfo:(void*)contextInfo;

// Accessor and mutator for an array of file types
- (NSArray *) fileTypesArray;
- (void) setFileTypesArray: (NSArray *) anArray;

// Accessor and specific mutator for the 'current directory', probably
// stored in NSUserDefaults somewhere
// Note that the class' +initialize method should set this up to be
// somewhere sensible
- (NSString *) userDirectory;
- (void) setUserDirectoryFromFilename: (NSString *) aFilename;

// Accessor for the window we want to attach the sheet to.
- (id) window;
...

.m file :
    
- (void) someMethod {
  NSOpenPanel * panel = [NSOpenPanel openPanel];

  [self setFileTypesArray:[NSArray arrayWithObjects:@"txt",@"rtf",
                                                    @"html",nil]];

  [panel beginSheetForDirectory:[self userDirectory]
                           file:nil
                          types:[self fileTypesArray]
                 modalForWindow:[self window]
                  modalDelegate:self
                 didEndSelector:@selector(filePanelDidEnd:
                                               returnCode:
                                              contextInfo:)
                    contextInfo:nil];
}

-(void)filePanelDidEnd:(NSOpenPanel*)sheet
            returnCode:(int)returnCode
           contextInfo:(void*)contextInfo {
  [self setUserDirectoryFromFilename:[sheet filename]];
  ...
}


----
Actually, beginSheetForDirectory is now documented. - Pietro Gagliardi <http://web.mac.com/pietro10 >
----

There is a bug in this class - if you run an NSOpenPanel with the filetype ".app" then it will Log the following error (FSOpenIterator failed:-1407) if you try an select an alias to a non-bundle app in the panel.

Try this for yourself in the "web" tab of the "internet" pane of the "System Preferences" App

----
This sort of thing really should be sent to http://bugreport.apple.com/ not the feedback page, which is more for user level feature requests. You need at least a ADC Online account to login.
--joey

yes, I filed this with BugReporter, it's bug no. 2828152 - digs.

----

Right now I'm doing this for the open and saveAs panels, and the NSSavePanel is always returning with a returnCode of -1000, whether the Save or Cancel button is pressed.  Needless to say, this is annoying.  Anyone know how to fix/get around that?

----
**Don't use an autoreleased object for contextInfo**

This one just bit me. If you're passing a contextInfo object to your panel's modal delegate you need to alloc/init or retain it, then release it at the end of your     didEnd... method. That goes for all contextInfo objects you pass to sheets. Because the apps run loop continues while the sheet is up, an autoreleased object likely will be gone by the time your     didEnd... selector gets called.


----


One useful undocumented NSOpenPanel method is <code>_setIncludeNewFolderButton:</code>.  Pass <code>YES</code> to add a "New Folder" button to the open panel, e.g.

    [openPanel _setIncludeNewFolderButton:YES];

This is especially useful when using a DirectoryOpenPanel, since if you are asking the user to choose a folder, they very often would want to be able to create a new one at that time.

----

Since when can you use:

    [openPanel _setIncludeNewFolderButton:YES];

it doesn't work for me. Neither does:

    [openPanel setIncludeNewFolderButton:YES];

----

It does work for me. I am using OS X 10.2.5 (v6L29). That's a nice bit of goodness! -- DustinVoss

----

This isn't undocumented anymore. Panther adds a **setCanCreateDirectories:** method. �DustinVoss

----

Is there anyway I can make my NSOpenPanel show
hidden files (i.e. those starting with a dot)?

*See http://www.cocoadev.com/index.pl?ShowingHiddenFilesInAnNSOpenPanel *

Also, is it me or is
    beginForDirectory:file:types:modelessDelegate:didEndSelector:contextInfo:
useless, since it requires a *docWindow* which it
doesn't take as argument!?!

*it just opens a modeless open panel. no doc window required.*

    


- (IBAction)showModelessOpenPanel:(id)sender
{
	NSOpenPanel *oPanel = [[NSOpenPanel openPanel] retain];
	
	[oPanel beginForDirectory:nil file:nil types:nil modelessDelegate:self didEndSelector:@selector(openPanelDidEnd:returnCode:contextInfo:) contextInfo:NULL];
}
- (void)openPanelDidEnd:(NSOpenPanel *)panel returnCode:(int)returnCode contextInfo:(void *)contextInfo
{
        if (returnCode == NSOKButton)
        {
	        NSArray *filesToOpen = [panel filenames];
	        //open files here
	}

	[panel release];
}



----

When I use the code at the top of the page, the sheet is resizable.  Sheets should not be resizable, correct?  How do you set it so it is not resizable.

----

What sheet? What code are you using? The     - (IBAction)showModelessOpenPanel:(id)sender method above shows an open panel as a modeless window, not a sheet. In any case, there's no rule against sheets not being resizable. Open or save panels **should** be resizable. If you don't want it to be for some reason, you can just say     [oPanel setShowsResizeIndicator:NO], which NSOpenPanel inherits from NSWindow.

---- 

I'm sorry, I was referring to the code at the very top of the page.  I just thought that it was odd to allow the user to resize the sheet, but if there's no rule against it, then that's fine.  By the way, the setShowsResizeIndicator if set to NO, will remove the indicator, but if the user tries to resize, they can still resize it.  I'll just call it good enough and allow them to resize.  I just hadn't seen a sheet that was allowed to do that.

*I fixed previous entry so it is more clear.*

----

In general, it pays to be lazy with this sort of thing. If the open panel you get from     [NSOpenPanel openPanel] is resizable, you can assume it's meant to be that way and just leave it. If the window  your NSDocument object creates is called "Untitled" instead of "untitled", you can assume that Apple will fix it in the future and just leave it.

----

We're having a strange problem where we can't click the sidebar area of the NSOpenPanel in our application.  We're unsure how to debug this.  Does anyone have an idea about why this would be happening?

*what's happening? the mouse physically won't move over there? you can in fact click, but it has no effect? what?*

Clicks in the sidebar have no effect.  Clicking in other areas of the open panel work fine.

**One would assume that by saying, "we can't click ..." the OP meant they tried to click. Specifically, it would be nice to know whether anything happens or nothing happens at all. I assume the latter. In which case, I'd suspect that if this is the only app that has that problem there may be an un-ended modal session in effect that's interfering. That is, however, just a guess. OP, please post the code responsible for opening the panel and answer whether or not you start any modal sessions anywhere in your app. Also, what (if anything) does happen when you click?**

----

I�m writing a tool which edits files normaly found inside a package, and so I�d like my open and save panels to allow browsing of package contents, i.e. have -setTreatsFilePackagesAsDirectories:YES called. Save panels can be customised in -[NSDocument prepareSavePanel:], but how can I customise the Open panel in an NSDocument-based app?

----
Hint: NSOpenPanel Inherits from	 NSSavePanel : NSPanel : NSWindow : NSResponder : NSObject

It is also handy to know that NSOpenPanel is a singleton.
----
This is doubtful as the documentation clearly states otherwise. The     +openPanel method is described as, "Creates and returns a new NSOpenPanel object."

----
  It is handy to know that "An NSDocumentController object manages an application�s documents. As the first-responder target of New and Open menu commands, it creates and opens documents and tracks them throughout a session of the application.'

So, either subclass NSDocumentController and implement - (int)runModalOpenPanel:(NSOpenPanel *)openPanel forTypes:(NSArray *)extensions to configure the provided  openPanel with the inherited -setTreatsFilePackagesAsDirectories:    [openPanel setTreatsFilePackagesAsDirectories:YES]; or if you are like me,
just call     [[NSOpenPanel  openPanel] setTreatsFilePackagesAsDirectories:YES]; from within -applicationDidFinishLaunching:.


----
Unfortunately, neither of those approaches actually works.

Er. On closer inspection the DocumentController approach works fine, if I use the passed openPanel rather than [NSOpenPanel openPanel]. A quick pointer comparison shows it isn�t a singleton. (Darn 4 am programming.)

----

an easy way to customize an NSOpenPanel to include a bit of your own stuff is with the following code:

assuming a custom view (in this case named "accessoryView") linked via an IBOutlet:

	NSOpenPanel* lPanel = [NSOpenPanel openPanel];
	[lPanel setAccessoryView:accessoryView];

this puts your custom view underneath the standard panel so you can add your own buttons, text, images etc.

EcumeDesJours

