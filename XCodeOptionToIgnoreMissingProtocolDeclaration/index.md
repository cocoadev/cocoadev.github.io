---
layout: page
title: XCodeOptionToIgnoreMissingProtocolDeclaration
---


Hello,
I'm not sure if this is the right place to post but here goes(if it doesn't belong here I can delete the page). 
I have the headers(using class-dump)  of Mail.App 3.0 - i intend to write a plugin.
I managed one plugin, however while trying to write another imported this file "General/MessageContentController.h" which has the following lines
    
#import "General/DocumentEditorManagingProtocol.h"

@class General/ActivityMonitor, General/AttachmentsView, General/EmbeddedNoteDocumentEditor, General/InvocationQueue, Message, General/MessageHeaderDisplay, General/MessageViewingState, General/NSArray, General/NSBox, General/NSButton, General/NSDictionary, General/NSImageView, General/NSLock, General/NSMutableDictionary, General/NSString, General/NSTextField, General/NSTimer, General/NSView, General/ObjectCache, General/TextMessageDisplay;

@interface General/MessageContentController : General/NSResponder <General/DocumentEditorManaging>
{
    Message *_message;
    General/ActivityMonitor *_documentMonitor;
    General/ActivityMonitor *_urlificationMonitor;
    id <General/MessageContentDisplay> _currentDisplay;
    id <General/MessageContentDisplay> _threadDisplay;



Now, I don't have access to General/DocumentEditorManagingProtocol.h nor the header declaring protocol General/MessageContentDisplay and General/XCode refuses to build (even when i removed the #import line)

Q: Is there a way to tell General/XCode to ignore the need for the protocol declaration (something like -undefined dynamic_lookup in linker flags, which I already have switched on)

Regards
Sapsi

Aah, include this line
@protocol General/DocumentEditorManaging,General/MessageContentDisplay;
Rgds
Sapsi
