---
layout: page
title: OReillyAddressBookTableExample
---

I've been looking at O'Reilly's Address Book articles by Mike Beam ([http://www.macdevcenter.com/pub/a/mac/2001/08/24/cocoa.html].  These don't deal with the Mac AddressBook app (although a later article in the series introduces it). Rather they are about making a simple address book using NSTableView. The articles also cover NSTableDataSource NSDictionary, saving data atomically, NSUserDefaults, XML etc.

Here's what Beam's original interface looks like:

http://www.macdevcenter.com/mac/2001/08/10/graphics/cocoa_interface.jpg

The source can be found near the end of this page: [http://www.macdevcenter.com/pub/a/mac/2001/08/10/cocoa.html?page=4]

I'm having trouble getting the thing to run after building it under Panther. Here is my log from debugging:

    
2004-06-19 16:29:34.026 AddressBook[576] *** -[NSCFString objectForKey:]: selector not recognized
2004-06-19 16:29:34.031 AddressBook[576] An uncaught exception was raised
2004-06-19 16:29:34.032 AddressBook[576] *** -[NSCFString objectForKey:]: selector not recognized
2004-06-19 16:29:34.033 AddressBook[576] *** Uncaught exception: <NSInvalidArgumentException> *** -[NSCFString objectForKey:]: selector not recognized
AddressBook has exited due to signal 5 (SIGTRAP).


----

I think you may have a NSUserDefaults collision of some form causing that error. You should probably hunt down the preferences file and delete it and launch it again.

Basically what that error means is that an NSString is being asked if it has an object for a key, aka the NSString is actually meant to be an NSDictionary. -- MatPeterson

----

Mat -- Yup you were right.  I trashed preferences and all is working fine.  Thanks very much!

----

**An attempt to update this example to use CocoaBindings**

This is a one-window app and it saves data in a plist.

I have embarked on the journey of trying to apply CocoaBindings to this little app.  And I have successfully added a new NSArrayController object to the Nib File window and created a new Controller Class called Entries, with its implementation file looking like this:

    
@implementation Entries

- (NSString *)firstname { return _firstname; }
- (NSString *)lastname { return _lastname; }
- (NSString *)emailaddress { return _emailaddress; }
- (NSString *)homephone { return _homephone; }


- (void)setFirstname:(NSString *)firstname
{
    [_firstname autorelease];
    _firstname = [firstname retain];
}

- (void)setLastname:(NSString *)lastname
{
    [_lastname autorelease];
    _lastname = [lastname retain];
}

- (void)setEmailaddress:(NSString *)emailaddress
{
    [_emailaddress autorelease];
    _emailaddress = [emailaddress retain];
}

- (void)setHomephone:(NSString *)homephone
{
    [_homephone autorelease];
    _homephone = [homephone retain];
}

@end



I've configured all the bindings and made all the proper connections to outlets so that everything works when using "Test Interface".  I can enter new values in either the table or the fields and everything gets updated.  And after entering a few rows of values in the table, whenever a row is selected its values will pop up in fields.  

BUT I'm not sure how to proceed so that this thing will load and save data from a plist as it did before I added all the Bindings stuff.  I've tried all sorts of combinations of using different parts of the original Controller methods, but nothing works. (I usually get an error message like this:  2004-07-15 01:56:39.930 AddressBook[628] [<Controller 0x37bd90> valueForUndefinedKey:]: this class is not key value coding-compliant for the key firstnameEntriesArrayControllerKey.)  First of all, should I even keep the original Controller class? Should I make my new Entries class the app controller and put saving and loading methods in there?  Where do I put the mandatory table data source methods (row count, column identifier, and the optional setObject)?  How do I make the plist data a table data source? Do I still need to use Table Column "Identifier" now that Bindings are in effect?  And a ton more questions .... as you can see, I'm pretty confused at this point.   Any help setting me straight would be greatly appreciated. 
 
----

The Entries class is your model layer class... If it were me, I'd name the class Entry though... 

You don't need to write any of the table view datasource/delegate code - the bindings do all that for you... same with the column identifiers

Just use keyed archiving... implement NSCoding in the Entry class (initWithCoder and encodeWithCoder)

You don't need the controller class, create a class that acts as your application delegate
this works for me... this is the implementation of my app delegate

    

- (id)init {
	// Saving to plist
    self = [super init];
	if (self != nil) {
	//    recordsFile = [NSString stringWithString:
@"~/Library/Application Support/AddressBookExperiment/MyExperimentalABookData.plist"];
		recordsFile = [NSString stringWithString:@"/tmp/thing.plist"];
		recordsFile = recordsFile stringByExpandingTildeInPath] retain];
		[[NSData *data = [NSData dataWithContentsOfFile:recordsFile];
		NSKeyedUnarchiver *unarchiver =
[[NSKeyedUnarchiver alloc] initForReadingWithData:data];
		records = unarchiver decodeObjectForKey:@"Objects"] mutableCopy];
		[unarchiver release];
		if ( nil == records ) {
			records = [[[[NSMutableArray alloc] init];
		}
	}
	return self;
}

- (void)save:(id)sender {
	NSMutableData *data = [NSMutableData data];
	NSKeyedArchiver *archiver =
[[NSKeyedArchiver alloc] initForWritingWithMutableData:data];
	[archiver setOutputFormat:kCFPropertyListXMLFormat_v1_0];
	[archiver encodeObject:records forKey:@"Objects"];
	[archiver finishEncoding];
	
    [data writeToFile:recordsFile atomically:YES];
	[archiver release];
}


just hook up the array controller in your nib like this in IB:
  binding:contentArray
  modelKeyPath: delegate.records
  Object Class Name (attributes inspector): Entry

then I add an action to the save menu to save in my app delegate (above)
I didn't do the save on edit thing you did in your previous example... it's possible to do, but it requires writing code... and I'm lazy  =)
note: you can save yourself about 3 lines of code by not saving as an XML plist

Ron
 
----

Wow!  Thanks for initiating me into the world of NSArchiver and NSCoding.

Although, I believe I need to iron out some niggling details before I can get to where I need to be.

I think I've done as you said, but in doing so the interface breaks.  In the bindings for the NSArrayController under contentArray, when I choose  Bind to:  SharedUserDefaults, and keep "values" that automatically pops up under Controller Key, and type in "delegate.records" under Model Key Path, the Interface works fine with "Test Interface", but not after compiling.  When clicking the "Add" button, I get the following error:  

2004-07-16 00:07:54.715 AddressBook[1161] *** -[NSUserDefaults setObject:forKey:]: Attempt to insert non-property value '<CFArray 0x336130 [0xa01900e0]>{type = mutable-small, count = 1, values = (0 : <Entries: 0x33e2e0>)}' of class 'NSCFArray'.

And nothing happens when I try typing into the table or the fields.  (I don't get any enabled field to type in.)

When I choose Bind to: Files Owner and type in "delegate.records" under Model Key Path, "Test Interface" does not allow me to type anything in the table or fields.  (I don't get any enabled field to type in.)  And the same is true after I compile it.

I've added the following to the Entries implementation (along with corresponding methods for its header):

    

- (id)initWithCoder:(NSCoder*)coder
{
	if (self = [super init])
	{
		[self setFirstname:[coder decodeObject]];
		[self setLastname:[coder decodeObject]];
		[self setEmailaddress:[coder decodeObject]];
		[self setHomephone:[coder decodeObject]];
		
	}
	return self; 
}

- (void)encodeWithCoder:(NSCoder*)coder
{
	[coder encodeObject:_firstname];
	[coder encodeObject:_lastname];
	[coder encodeObject:_emailaddress];
	[coder encodeObject:_homephone];
	
}



And I've created a new class with your coding in the implementation, along with this in its header:

    

   @interface AppHelper : NSObject {
	
    NSMutableArray *records;
    NSString *recordsFile;	

}

- (void) save:(id) sender;



I still can't get bindings to work for this simple app.  Seems like it should be a simple thing, but I think it may be because it's a non-document based app, and all the Bindings examples I've ever seen are document-based.  I'm not exactly clear on the issues, but I think it has something to do with the connections to the File's Owner.

----

Editor's Note (January 2006)

Actually, I think that http://www.cocoadevcentral.com has a very useful introductory example showing how to use CocoaBindings with a non-document app via the use of an NSObjectController that links the view controller with a hierarchy of NSArrayController instances. Quite useful, if you're just learning.

The specific link at cocoadevcentral is http://www.cocoadevcentral.com/articles/000080.php

