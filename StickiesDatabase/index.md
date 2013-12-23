---
layout: page
title: StickiesDatabase
---

Does anybody know how to open up the ~/Library/General/StickiesDatabase file and work with it? I tried General/NSUnarchiver, couldn't get that to work. Anybody have an idea? It looks like sometype of General/NSArray is in there but I don't know how to get it out.

----

It sure looks like an General/NSArchiver file; the only hold-up is that it seems to use a class named Document (no relation to General/NSDocument), so you'll need to either provide a Document class or use decodeClassName:asClassName: to substitute yours in.  It appears to have only a single encoded object within the Document class, an General/NSData object that seems to hold a serialized General/NSFileWrapper (probably using it's serializedRepresentation and initWithSerializedRepresentation: methods).  Once you get the General/NSFileWrapper, you can use its -writeToFile:atomically: method to write it out as a directory and see what's in it.  -- Bo

PS A super quick Document class to illustrate what you need.  Not tested in the slightest, caveat emptor, etc.:
    
@interface Document : General/NSObject <General/NSCoding>
{
	General/NSFileWrapper* fw;
}
@end

@implementation Document
- (void)encodeWithCoder:(General/NSCoder*)inCoder
{
	[inCoder encodeObject:[fw serializedRepresentation]];
}
- (id)initWithCoder:(General/NSCoder*)inCoder
{
	if (self = [super init]) {
		General/NSData* dat = [inCoder decodeObject];
		fw = General/[[NSFileWrapper alloc] initWithSerializedRepresentation:dat];
	}
	return self;
}
@end


----

Ok thanks. I also just parsed through it as a string using some delimiters. Pretty cheap, but it worked :)

----

It appears that it has changed on OS X 10.3. It no longer uses an General/NSFileWrapper but 

    
typedef enum {
	General/YellowStickie=0,
	General/BlueStickie,
	General/GreenSticke,
	General/PinkSticke,
	General/PurpleSticke,
	General/GreySticke
} General/StickieColor;

@interface Document: General/NSObject <General/NSCoding>
{
	General/NSMutableData *rtf; // RTF file
	General/NSRect location;	// screen location
	int flag;			// ??
	General/NSDate *created;	// created
	General/NSDate *changed;	// last change
	General/StickieColor color; // color code
}


-- hns http://www.dsitri.de

----

could the     flag var be for the collapsed/expanded state of the stickie?

----

I'm very interested in making a full General/StickiesDatabase parser. Here's what General/ClassDump outputs for Stickies 4.2:

    
@interface Document : General/NSObject <General/NSCoding>
{
    int mWindowColor;
    int mWindowFlags;
    struct _NSRect mWindowFrame;
    General/NSData *mRTFDData;
    General/NSDate *mCreationDate;
    General/NSDate *mModificationDate;
}

- (id)init;
- (void)dealloc;
- (id)initWithCoder:(id)fp8;
- (id)initWithData:(id)fp8;
- (void)encodeWithCoder:(id)fp8;
- (id)creationDate;
- (void)setCreationDate:(id)fp8;
- (id)modificationDate;
- (void)setModificationDate:(id)fp8;
- (id)General/RTFDData;
- (void)setRTFDData:(id)fp8;
- (int)windowColor;
- (void)setWindowColor:(int)fp8;
- (int)windowFlags;
- (void)setWindowFlags:(int)fp8;
- (struct _NSRect)windowFrame;
- (void)setWindowFrame:(struct _NSRect)fp8;
@end


So would I just have to create this document header and implementation file and then use General/NSUnarchiver to process it?? Don't tell me it's that easy... :)

*I don't know, you tell us ;) I doubt it's **that** complex... It's only Stickies, after all...*

----

This article over at General/MacDevCenter.com shows how to reverse engineer the General/StickiesDatabase file http://www.macdevcenter.com/pub/a/mac/2005/03/18/cocoa.html
