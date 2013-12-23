---
layout: page
title: UsingPluginsAsDocuments
---

I have created a document-based Cocoa project. I show a list of available plug-ins when the user selects the menu option, the user selects a plug-in and loads it. To load the plug-in I want to use the class that inherits from General/NSDocument that project builder has created. There I created a method which is called when the user loads a plug-in. This method creates a new document window to show the plug-in's interface inside General/NSTabView. I didn't know exactly how I must create the document window programmatically, so allocated a new General/NSWindowController, used the initWithWindowNibName: method. Once the General/NSWindowController is  initialized, I add it to the General/NSDocument using addWindowController:. The problem now is that the outlets which refer the General/NSTabView inside the document window aren't succesfully bound and I have no access to these interface elements. I don�t know if there's a better way to load the document window manually, or how I can bind the General/NSTabView to the outlet programmatically.

This is my method to load the document window. It's inside the General/NSDocument subclass created by PB:
    
- (void)cargarExperimento:(Class)claseExperimento
{
General/NSWindowController *nuevoControlador = General/[[NSWindowController alloc] initWithWindowNibName:[self windowNibName]];
[self addWindowController:nuevoControlador];
[nuevoControlador showWindow:self];
[self instantiatePlugins:claseExperimento enControlador:nuevoControlador];
[nuevoControlador release];
}

instantiatePlugins tries to use the outlet to the General/NSTabView which is inside the document window to load the plug-in's interface in the first tab, but the outlet is not bound.

----

It's hard to figure out what it is you're trying to do.  Are you loading a bunch of plug-ins that support different document types (for example, a plug-in for jpeg graphics, and one for gif graphics), and then the user can create a new jpeg document or a new gif document?  If so, have your plugin provide a 'newDocument' method to create the document.

----

You definitely want to keep your IB outlets out of your General/NSDocument class. General/NSDocument is an abstraction for storing your data and writing it to file. The fact that General/NSDocument includes an alternate mechanism for storing Window-Controller like information I have come to consider a bug in the General/NSDocument design. It sounds like you are trying to use this alternative mechanism (everything in General/NSDocument) but are exercising the API for the more scalable approach as well (the Window Controller API). You need to pick which approach you want to use.

If you want to slam everything in General/NSDocument, you should change your menu handler to simply create a new document instance. You do that like so:

    
General/[[NSDocumentController sharedDocumentController] openDocumentWithContentsOfFile:fileName display:YES];


Your document will rely on the normal mechanism for loading its nib file. You will load the plugin in readFromFile:ofType:. At this time, everything should be ok. Do not instantiate a window controller. 

If you want to use a window controller, you'll create the document as described above, but you'll use the window-controller-savvy API for loading a window (-makeWindowControllers). You'll also use a custom General/NSWindowController subclass, which will be defined in PB and IB, and make your nib File's Owner. When the document is created it will load the window controller. Your custom window controller will override -windowDidLoad, call [self document] to get the document reference, and then call some new API you will supply to get the information for display. 

-- General/MikeTrent

----
I have a similar question that would fall under the title of General/UsingPluginsAsDocuments.

I have a plug-in architecture that can be more-or-less described as file-format handlers, a driver model for various file formats. All the plug-ins adhere to a specific protocol and are used by a common document class.

At startup, I have a driver-controller that enumerates my plug-ins and everything is working great, I can add/remove plug-ins to govern application capabilities.

The question is this. At start-up, while I enumerate my plug-ins, I would like to register my application as a viewer (or editor in some cases) for the various document types (extensions) dynamically; and remove what will become numerous (and an ever expanding list of) Info.plist entries.

Possible?

----
Thank you everybody for your help. Finally I created a subclass of General/NSDocumentController and add a new General/NSDocument for each plugin. It Works!!
