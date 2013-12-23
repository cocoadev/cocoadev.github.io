---
layout: page
title: CanIChangeAFileStructureForAnNSDocument
---



I have written a simple NSDocument-based program in Cocoa/Obj-C. Now, I've realized that I should include other information in the document. Initially, there were two dictionaries of data: One is a day-by-day schedule, while the other is a series of objects containing various bits of other information. Version 2 will contain these pieces along with another dictionary of information. The documents are saved by basic coding then archiving routines, as described by Apple. My questions are: (1) If I load an "old" document into the new version, what happens? Will the program simply generate empty data structures for those pieces that aren't included when I     initWithContentsOfFile? (2) If not, how can I even load an old file so I can convert it to a new type? Must I make a whole document class to match the original as well as the document class to match the current version? For this purpose, the new file types will always contain all the elements of the old file type, plus others. Thanks for any insight. I've managed to cobble together some code that allows the "new" program to load "old" files, but I don't know if it's working because of luck or because it's basically OK to load files that are missing pieces as long as one doesn't try to ACCESS those pieces.... 

Thanks! -dan

----

Are you archiving using an NSKeyedArchiver and the encodeBlah:forKey: methods? If so, then, in your unarchiving code, you can check to see what decodeBlahForKey: is returning (if a value for the specified key doesn't existing in the archive, it will return nil) and figure out what type of document it is.

I usually encode a version number into archives with a key like @"ArchiveVersionKey". Then, when unarchiving, I grab the value for that key and do different things depending on what version I get back. In your case, you could check for the existance of a non-nil value for one of your new elements, and know if you're dealing with an old or new file, and then do the appropriate thing (big "if" statement).

- ChrisCampbell

----
I seem to be using     return [NSArchiver archivedDataWithRootObject:self]; as the archiving method. In individual classes, I use     [coder encodeObject:firstName] etc., where firstName is a pointer to an NSString. I guess I should read more about keyed archiving, as it sounds like what you've written is akin to what I actually need. Any idea what the consequences would be if I simply kept this general form, though? Does OS X simply ignore that which it can't find? If I don't try to access things that weren't encoded, can I avoid treachery this way, too? As I vaguely recall, If you decode things without a keyed archiver, they must be decoded in the same order in which they were encoded. If fewer objects are encoded than are expected, but they match the corresponding elements' order until that point, do the rest just become nil, as you mentioned above? That sounds like it won't break the program, at least....

Thanks again for your insight!

-dan

OK, so I switched everything over to keyed archives, but I still can't figure out how to get the program to read the older, unkeyed archives. If I use an     NSKeyedUnarchiver to try to unarchive an unkeyed archive, the program raises exceptions because it can't create a CFPropertyList from some encoded XML data. Is there a way to query the data to find out whether a keyed or unkeyed archiver was used? If not, how can I deal with this situation? I can't ask the keyed decoder to look for a version number since it can't get that far...
-d
----

*Quick and dirty: change the extension/file type for the new keyed archives. Choose a load method accordingly. Ugly but easy. Anyone suggest a better answer?*

----

OK, sorry to answer my own question, but here's the workaround I've devised. It hasn't been tested thoroughly, so the assumption I make about how these archives are serialized is tenuous. Anyone with some free time could check to see if their data object conforms to the follow specs:

    
- (BOOL)loadDataRepresentation:(NSData *)data ofType:(NSString *)aType
{
    // Insert code here to read your document from the given data.  You can also choose to override -loadFileWrapperRepresentation:ofType: or -readFromFile:ofType: instead.
    if ([aType isEqualToString:@"DocumentType"])
	{
		iCallDocument *theNewDocument;
		NSRange myRange;		
		
		// Check the NSData data to see what type of archive this is... Keyed or Unkeyed.
		// Keyed archives seem to start off with Hex 62706c69, whereas unkeyed archives seem
		// to start with 040b7479. Let's try that to determine what type of unarchiver to use.
		
		myRange.location = 1;
		myRange.length = 8;				// The search parameters for the NSData object.
		NSLog (@"Here's the string I got: %@", data description] substringWithRange:myRange]);


Anyhow, the upstart is that if the data passed to this function start with the first hex signature, it seems that we're decoding a keyed archive, so a conditional instead of the [[NSLog line will order an NSKeyedUnarchiver. You get the picture. Now, can anyone else verify that all keyed archives reliably start with <62706c69 (I've set the range to ignore the leading less than sign), whereas unkeyed archives start <040b7479? Thanks again for your interest and support!

-dan

----

I would really not recommend the above approach. Without knowing Apple's file format, it's not possible to say whether those numbers will change in the future.

It would be possible to simply try the keyed unarchiving, then catch the exception and retry with unkeyed. However, this relies on the exception always being thrown. This is probably a better bet than the above, but it's still a little risky.

The best bet would be to throw in your own magic number of some kind. For example, when you archive, get the data from the archiver and then prepend some short identifier to it. When you unarchive, look for the presence of this identifier and use your old methods if it's not there. You can do similar variations with checksums or something like that. Although Apple's format is opaque, nothing prevents you from tacking stuff on to it, as long as you strip it back off before you hand it back to Apple's code.

----
Unfortunately, the program has been writing files in the old, unkeyed format for some time, so those will have to be identified by the unkeyed signature. Is this where I'd add my own "signature?" I'm not sure where I can alter the data after it's serialized but before it's written...

    
- (NSData *)dataRepresentationOfType:(NSString *)aType
{
    if ([aType isEqualToString:@"DocumentType"])
	{
                // --- modify this line to send data with prepended signature???
		return [NSKeyedArchiver archivedDataWithRootObject:self];  
	}
	NSLog (@"That was an invalid type to attempt to archive. (iCallDocument dataRepresentationOfType:aType false)");
	return nil;
}


----

There isn't a problem. You can put an identifier on your new files. If the file has no identifier, then it's one of the old ones.

As for your other question, you'd do something like this:
    
NSMutableData *data = [NSMutableData data];
[data appendBytes:"signature" length:9];
[data appendData:[NSKeyedArchiver archivedDataWithRootObject:self]];
return data;

Do the reverse on the backend using     -subdataWithRange:.

----
Sounds simple enough! I'll give it a try. Thanks! The code snippet really helps me; I'm still quite new to Obj-C, and my aging brain doesn't learn new things like it used to!
-dan
----
    
const char signature[] = "cstringkeyedsignature"; // C chars are 1 byte each
const int sigLen = strlen(signature) + 1; // You now have to include "strings.h"
// The "+ 1" is to include the null byte at the end of a C string.

- (NSData *)dataRepresentationOfType:(NSString *)aType
{
    if ([aType isEqualToString:@"DocumentType"])
	{
		NSMutableData *data = [[NSMutableData alloc] initWithBytes:signature length:sigLen];
		[data appendData:[NSKeyedArchiver archivedDataWithRootObject:self]]; // Add the archived data
		return [data copy]; // Immutable copy 
	}
	NSLog (@"That was an invalid type to attempt to archive. (iCallDocument dataRepresentationOfType:aType false)");
	return nil;
}

- (BOOL)loadDataRepresentation:(NSData *)docData ofType:(NSString *)docType
{
	char docSignature = calloc(sizeof(char), sigLen);
	[docData getBytes:docSignature length:sigLen];

	if (docSignature[sigLen - 1] != '\0') { // If the last byte of the document signature is not 0
		// The "signature" is not a C string, so it can't be your signature.
		// So this archive is unkeyed.
	} else if (strcmp(docSignature, signature) != 0) { // If the two signatures are not equal
		// The document's signature is a C string, but it's not the same as YOUR signature string
		// So this archive is unkeyed...or it's an older version, if you start changing the signature.
		// If you do change the signature, make sure it's always the same number of ASCII bytes (same length string)
	} else {
		// They're equal, so it must be your keyed archive signature.
		NSRange dataRange = NSMakeRange(sigLen, [docData length] - sigLen);
		NSData *data = [docData subdataWithRange:realDataRange];

		self = [NSKeyedUnarchiver unarchiveObjectWithData:data]; // Not sure if this is how you do it.
		return (self != nil);
	}
}

I think this will work...but note that I'm not the person who suggested the idea, so I could easily be wrong. Note that since the old archives don't have any signature (unless you are going with Apple's private signature), you have to make sure not to cut anything off (using     subdataWithRange:) that needs to be there for NSKeyedUnarchiver to work. --JediKnil
----
Again, thanks to everyone for their suggestions. I basically did what you guys said, but I converted the data back to an NSString so I could use     isEqualToString: for the comparison between the first n bytes and the expected signature CString. Seems to work.... Only time will tell for sure. 'Course, it would've been nice if there was a convenient way to identify keyed v. unkeyed archives without these machinations. Even nicer if the NSKeyedUnarchiver would simply notify us that it's dealing with an unkeyed archive. At least this solves the problem in an apparently reliable way. If anyone wants the final code, drop me an e-mail through www.ascendiac.com > feedback. -dan

----

I'd try to use an NSKeyedUnarchiver and, if it fails (by raising an exception), switch to an NSUnarchiver.

    
id obj = nil;

NS_DURING
    obj = [NSKeyedUnarchiver unarchiveObjectWithData:data];
NS_HANDLER
    obj = [NSUnarchiver unarchiveObjectWithData:data];
NS_ENDHANDLER

// Do whatever you want with obj now


The contents of your archive should not *replace* your NSDocument-subclass object. You should copy data from the archive into instance variables in the NSDocument-subclass object. In the code above, the next few lines would do something like:

    
   [self setWidget:obj];
   [self updateWidgetTextFields];


- ChrisCampbell

----
For what it's worth, here's the code. It seems to work, but if there's an obvious error, please let me know! -dan
    
- (BOOL)loadDataRepresentation:(NSData *)data ofType:(NSString *)aType
{
    if ([aType isEqualToString:@"DocumentType"])
	{
		iCallDocument *theNewDocument;
		NSRange myLocalRange;
		myLocalRange.location = 0;
		myLocalRange.length = 12;				// The search parameters for the NSData object.
		NSString *myDataSignature = [[[NSString alloc] initWithData:[data subdataWithRange:myLocalRange] encoding:NSUTF8StringEncoding] autorelease];
		
		// Check for the presence of the "keyed archive" signature added to all files created after version 1.3. Since the two types of unarchivers
		// (keyed v. unkeyed) are not compatible with one another, we must choose the appropriate one ahead of time.
		
		if ([myDataSignature isEqualToString:@"keyedArchive"])
		{
			// First, modify the myLocalRange to exclude the signature:
			myLocalRange.location = 13;						// Start at byte 13 (the 14th byte). Need to skip the null terminator, too!
			myLocalRange.length = ([data length] -13);		// Correct the length to match the data.

			// Now, send the data without the signature bytes to the NSKeyedUnarchiver:
			theNewDocument = [[NSKeyedUnarchiver unarchiveObjectWithData:[data subdataWithRange:myLocalRange]] retain];
			if (theNewDocument)
			{
				[self setTheMasterSchedule:[theNewDocument returnTheMasterSchedule]];
				[self setTheOtherInfoDictionary:[theNewDocument returnTheOtherInfoDictionary]];
				return YES;
			}
			else
			{
				NSLog (@"Failed to unarchive the Keyed archive. iCallDocument > loadDataRepresentation");
				return NO;
			}
		}
		else
		{
			// This is data from the serial, unkeyed archiver. Simply send the data untouched to the unarchiver.
			theNewDocument = [[NSUnarchiver unarchiveObjectWithData:data] retain];
			if (theNewDocument)
			{
				[self setTheMasterSchedule:[theNewDocument returnTheMasterSchedule]];
				[self setTheOtherInfoDictionary:[theNewDocument returnTheOtherInfoDictionary]];
				return YES;				
			}
			else
			{
				NSLog (@"Failed to unarchive the Unkeyed archive. iCallDocument > loadDataRepresentation");
				return NO;
			}
		}
		// Both types of unarchivers failed. Post a notice!
		NSLog (@"Could not determine if the archive was keyed or not. Failed at: loadDataRepresentation:(NSData *)data ofType:(NSString *)aType.");
		return NO;
	}
	NSLog (@"Could not unarchive the file. Failed at: loadDataRepresentation:(NSData *)data ofType:(NSString *)aType, from if ([aType isEqual...]).");
	return NO;

}


----
A note on trying the keyed archiver and catching the exception, even when caught, CFLog(0) dumps several lines to stderr: 

**
CFLog (0): 
	CFPropertyListCreateFromXMLData(): plist parse failed; the data is not proper UTF-8. The file name for this data could be:
	-filename-
	The parser will retry as in 10.2, but the problem should be corrected in the plist.
**

Not necessarily the end of the world, but if you're reading many files in a tight loop, can get ugly.

