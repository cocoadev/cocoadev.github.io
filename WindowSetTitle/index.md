---
layout: page
title: WindowSetTitle
---

see also General/SettingWindowTitle

In a non-document-centered app, you can subclass     General/NSWindowController.  Inside that class, use the following code to set the title of the window which that class controls:

    General/self window] setTitle:@"testtitle"];

----
**Document-centered apps put an icon for the document type in the window's title bar.**

I have an     [[NSWindow in a regular Cocoa app (not document based). I want to put an     General/NSImage in the title. But these lines of code don't work. They say "Too many arguments to function."
    
 General/NSImage *card = General/[NSImage imageNamed:@"card"];
 General/self window] setTitle:@"%@ [[FlashCard", card]];

Any thing that I did wrong?
-- General/JoshaChapmanDodson

----
You need an General/NSDocument window. There is no other way of producing the image in the toolbar (except see just below in this reply, if your image is a file icon).

Besides that, you are using the     setTitle: method improperly.

It is not possible to set an     General/NSImage as a window's title? The method is     - (void)setTitle:(General/NSString *)aString. Even if your code worked, you'd just get a textual representation of the image data.

Strings can only contain strings, not other arbitrary objects like images (I know I'm being very specific, there are other instances.)

Of course, if your image is a file icon, you can use     - (void)setTitleWithRepresentedFilename:(General/NSString *)path and you'll get your filename and icon, with the nice command-click dropdown into the filesystem. This worked fine for me, even though my app's not formally document-based.

----

**File's Owner issues with General/NSWindowController and General/NSDocument subclasses**

**Overriding     windowTitleForDocumentDisplayName: in the window controller**

My application uses the document-centered architecture. I am having a problem with the title of the window in my application. I checked the documentation and it said I could use the setTitle method of General/NSWindow to set the title. But when I write any one of the three lines of code below within     windowControllerDidLoadNib or     awakeFromNib I get a bunch of warnings. Although the program does work, I get the title that I set in IB.

    

[self setTitle:@"General/MyApplication"];

[window setTitle:@"General/MyApplication"];

General/[[MyDocument window] setTitle:@"General/MYApplication"];

2005-07-18 17:15:20.675 General/SoundMaker[23061] *** -General/[MyDocument setTitle:]: selector not recognized [self = 0x328640]

2005-07-18 17:15:20.676 General/SoundMaker[23061] An uncaught exception was raised

2005-07-18 17:15:20.676 General/SoundMaker[23061] *** -General/[MyDocument setTitle:]: selector not recognized [self = 0x328640]

2005-07-18 17:15:20.676 General/SoundMaker[23061] *** Uncaught exception: <General/NSInvalidArgumentException> *** -General/[MyDocument setTitle:]: selector not recognized [self = 0x328640]

General/SoundMaker has exited due to signal 5 (SIGTRAP).



When I place the same line in windowControllerDidLoadNib, my application displays the title I set in IB instead of the one in the code.

----

It looks like you're trying to set the window's title, but that you're actually telling your document to change its title (documents don't have titles, hence the "selector not recognised" errors).

Furthermore, your app is document based and the window title is set automatically in a document based app (while ignoring setTitle calls as well).  Also, "[self setTitle...]" won't work unless the class you are editing has a method called setTitle, which it looks like it doesn't.

To change the title in a document-based application, you have to subclass General/NSWindowController and to override the method

- (General/NSString *)windowTitleForDocumentDisplayName:(General/NSString *)displayName

Create a new instance of the controller subclass in the General/MyDocument method - (void)makeWindowControllers and add the controller to the document with - (void)addWindowController:(General/NSWindowController *)aController (do not override the - (General/NSString *)windowNibName method). 

----

I still get warnings, and my application does not even load the window after adding these lines. Please tell me what I did wrong in following the instructions.

I created a new Objective C General/NSWindowController Subclass called General/TitleChange

General/TitleChange.h was left as it is.
In General/TitleChange.m I added the following lines.

    
- (General/NSString *)windowTitleForDocumentDisplayName:(General/NSString *)displayName
{	
	return @"My Application";
}


In General/MyDcoument.h I added the line
    
General/TitleChange *titleChange;


In General/MYDocument.m I added these lines
    
- (void)makeWindowControllers
{
	titleChange = General/[[TitleChange alloc] init];
	[titleChange addWindowController:titleChange];
}


After this I commented out the entire - (General/NSString *)windowNibName method). The only line inside it was return @"My Document";

I get the following warnings 

    

General/MyDocument.m:314: warning: '...' as arguments.)

General/MyDocument.m:314: warning: will be assumed to return 'id' and accept

General/MyDocument.m:314: warning: (Messages without a matching method signature

General/MyDocument.m:314: warning: 'General/TitleChange' may not respond to '-addWindowController:'



Apart from this the application window does not load and displays the folowing in the run log.
    
2005-07-20 13:19:00.880 General/HavingFun[7238] *** -General/[TitleChange addWindowController:]: selector not recognized [self = 0x342aa0]
2005-07-20 13:19:00.886 General/HavingFun[7238] *** -General/[TitleChange addWindowController:]: selector not recognized [self = 0x342aa0]


----

You probably should name your window controller subclass more generally, but you can ignore this advice until you get your feet under you in using the document architecture. For the time being....

Be sure that the - (id)init method of General/TitleChange is something like

- (id)init
{
	if (self = [super initWithWindowNibName:@"My Document"]) {
		...
	}
	return self;
}

In - (void)makeWindowControllers you have to change 
	[titleChange addWindowController:titleChange];
to
	[self addWindowController:titleChange];

----

Thanks a lot. I did that and I got the desired title.

Although my window loaded from the nib, none of its buttons or outlets were working. I figured that the reason for this was the fact that the Window Controller is the new File's Owner. Since all my actions were connected to the File's Owner which was My Document previously, they obviously wouldn't work. 

To get around this I instantiated the General/MyDocument class in IB and reconnected the outlets and actions to it. The program was working fine. Also I wanted to perform some actions before the program could be used so I put them in awakeFromNib in General/TitleChange. 

One more question, if I want two or more windows(or panels) to start with the program, how do I do it? I understand that it can be easily done through General/TitleChange(the Window Controller) but what if some methods in the General/MyDocument class need to send messages to it, for example to write something to a General/NSTextField. is there a way to send a message from one class to an instance of a class inside another class? 
General/MyDocument-->General/TitleChange-->The extra display window(an instance inside Title Change with its own class)

Any suggestions on if this was the correct approach or not would be greatly appreciated.

----

You absolutely **should not** instantiate the General/NSDocument subclass in General/MyDocument.nib, particularly if you have established a subclass of General/NSWindowController. The proxy File's Owner is there to represent either your document or window controller subclass for the purpose of connecting view objects to their outlets or code; if you use a window controller, you can always get a reference to the document from it. In any event, you have created a subclass of General/NSWindowController, you should switch the class reprented by File's Owner to your window controller's class. You almost have it together. Really, keep trying. You should implement the actions for your window's controls in one subclass or the other, and to connect the actions via File's Owner.
