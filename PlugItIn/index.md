---
layout: page
title: PlugItIn
---



Recently I came across a problem with the plug-in architecture I'm using.
The plugins load a view into a document, but the problem is that I'm using an NSApplication delegate to load, initialize and instantiate my plugins. But, in a document-based application the file's owner is an NSDocument subclass, and you can't hook up from one NIB to another (MainMenu.nib to MyDocument.nib, that is)
Any help?

----

Well, first of all, you really should have a separate controller for your plugins.

You can get a pointer to the frontmost document with     [[NSDocumentController sharedDocumentController] currentDocument] and insert your plugin's view from there. Maybe I'm not seeing what the problem is - what do you need to make connections between nibs for?

----

I have a class that extends NSApplication through a delegate. That classes's purpose is the plugin architecture.
To make it a delegate though, NSApplication needs to connect to it, therefore, they need to be in the same NIB.
But the delegate contains several outlets to NSBoxes (and the document window). The plugins that the delegate loads are supposed to be able to change the contents of those NSBoxes with an NSView inside a NIB inside the plugin bundle.
I need the connection to the NSBoxes to message them.

----

I think FormalProtocols are the answer you're looking for. Here's how I'm doing it in my plugin/document-based app:

I have a shared PluginController class (see SingletonDesignPattern) that is responsible for loading and maintaining my plugins. The plugins conform to a PluginProtocol, which declares methods for my app and document classes to use to get information about the plugins such as the name to display in the UI, the data the plugin is capable of acting upon, the selector to call when the plugin is invoked, etc.

My Documents conform to a DocumentProtocol, which declares methods the plugin can use to access stuff in my document - views, document data, and so on.

I don't know if this is the best architecture for you (or even for me! see my PluginAPIDesign page) but it seems to be working well so far.

Also, I would just pass data between the plugin & document. Have a     setIndicatorImage:(NSImage *)anImage method in the DocumentProtocol (or whatever, depending on what the contents of the view you're now passing from the plugin represents)) and have the plugin call that whenever it needs to change the image, rather than directly setting a view in the document.

----
I wasn't really looking at the situation the right way, so I didn't know what to do, and I'd of never figured out     [[NSDocumentController sharedDocumentController] currentDocument]
One last question:
Say that my subclass of NSDocument now has those outlets instead of the delegate. I will have functions to access those outlets.
But how should I return those outlets?
I was thinking something like:
    
- (NSBox *)getmainBox;
{
    return &mainBox;
}

Assume that mainBox is an IBOutlet directed towards an NSBox.
Would that be correct?
I feel really stupid asking this, but it just escapes me at the moment... (Excuses, excuses)

----

mainBox is a pointer, and you want to return a pointer, so you'd just do something like

    
-(NSBox *)mainBox
{
    return mainBox retain] autorelease];
}


outlets are just ivars - there's nothing special about them except their initial values are set in IB.

----

Hmm in my app I don't use protocols just an [[NSObject subclass that the plugins use. Is it better to use protocols? That'd offer much more flexability to the developer of the plugin right??

 It formalizes the contract between the application and the plugin -- it's not really about flexibility. The API itself may or may not be flexible. But if you want the assurance that a plugin will respond to messages which you send to it, and would rather send one message (-conformsToProtocol) than many (-respondsToSelector: for each selector) messages, a protocol is the best way to go. If you set it up correctly, you can make it so that the plugin always responds to the app (itself reacting to the user) instead of the plugin pestering the app for things when it might not be prudent. The plugin then acts like a delegate: ask it what data it wants to work on with one message, send it that data with another message, ask it for results (unless it has its own UI and can present them independently) etc.

----
Yay boxes! Everything is working, which is go-
WAIT A MINUTE!
Something isn't. 9 ERRORS AND 4 WARNINGS????
Something strange...
all of the errors would be explained by the interface not working. And the last two errors have to do with the interface file itself.
"Parse error before 'interface'"
"Parse error before }"                  
Soooo....                
What's wrong with this code: 
    

#import <Cocoa/Cocoa.h>
#import "PluginInterface.h"

@interface DBappdelegate : NSObject {

        NSWindowController* theWindowController;        //	the main window's controller
	NSMutableArray* pluginClasses;			//	an array of all plug-in classes
	NSMutableArray* pluginInstances;		//	an array of all plug-in instances
}
- (void)activatePlugin:(NSString*)path;
- (void)instantiatePlugins:(Class)pluginClass;
- (id)init;
@end

???

*Could be something with PluginInterface.h.. *

Yeah, but all the errors happen in my delegate. And I can't find anything wrong with PluginInterface.h
Found something: the interface is in the file DBappdelegate, and it's     @implementation DBappdelegate
changed it, still errored.

