---
layout: page
title: HackingKeyedArchiverFiles
---

I am really wanting to automatically generate iMovie projects based on content/settings in an application.  Unfortunately, iMovie has no AppleScript support, so that route is of no help.

My thought was to try to create the file "iMovie Project.rcproject/Project" automatically so that iMovie could recognize it.  To do this, I was going to need to be able to write the ./Project file myself in the app.  And the first step to do this, since the file format is not documented, is try to hack up the file format and figure out where all the data is stored.

In order to do this, I made a new Document-based Cocoa app to start hacking the file up.  And in the Document class I added the following method to try to read the ./Project file:

    
- (BOOL)readFromData:(NSData *)data ofType:(NSString *)typeName error:(NSError **)outError
{
	// load from data
	NSKeyedUnarchiver *_unarch = [[NSKeyedUnarchiver alloc] initForReadingWithData:data];
	id _dat = [_unarch decodeObjectForKey:@"root"];
	
	// log
	// NSLog(@"Data:\n\n%@", _dat);
	
	// loaded
	return YES;
}


At first I was getting errors in the Console about not being able to instantiate certain classes and such.  By looking at the ./Project file in Property List Editor, I could make some sense of the class hierarchy for most of these classes, and in most cases they seem to just extend NSObject.  So, the next step was to create new classes for all of these, and have them extend NSObject (and implement the NSCoding prototype).

And now the problem, when in the initWithCoder: method for any of these new classes, I can't seem to get at any data for those objects.  I've tried to look at the raw NSKeyedArchiver data in the ./Project file, but the file format is so confusing I have no idea what is going on.  I did my best to try to match up some keys for some of the data for these objects, but none of them had any data (according to NSKeyedArchiver).

This is some of the code I tried to use:

    
- (id)initWithCoder:(NSCoder *)decoder
{
	self = [super init];
	
	// does it have any of these?
	NSLog(@"width: %d", [decoder containsValueForKey:@"width"]);
	NSLog(@"height: %d", [decoder containsValueForKey:@"height"]);
	NSLog(@"duration: %d", [decoder containsValueForKey:@"duration"]);
	
	return self;
}


I did this on many different classes with many different keys, and none of them had values for any of the keys:

    
09-02-24 2:07:34 PM Data Loader[2925] startOffset: 0 
09-02-24 2:07:34 PM Data Loader[2925] data: 0 
09-02-24 2:07:34 PM Data Loader[2925] titleName: 0 
09-02-24 2:07:34 PM Data Loader[2925] duration: 0 
09-02-24 2:07:34 PM Data Loader[2925] titleNibName: 0 
09-02-24 2:07:34 PM Data Loader[2925] anchorTargetOffset: 0 
09-02-24 2:07:34 PM Data Loader[2925] aspectRatio: 0 
09-02-24 2:07:34 PM Data Loader[2925] enabled: 0 
09-02-24 2:07:34 PM Data Loader[2925] anchorItem: 0 
09-02-24 2:07:34 PM Data Loader[2925] text1: 0 
09-02-24 2:07:34 PM Data Loader[2925] graphicsColor: 0 
09-02-24 2:07:34 PM Data Loader[2925] text2: 0 
09-02-24 2:07:34 PM Data Loader[2925] NSParagraphStyle: 0 
09-02-24 2:07:35 PM Data Loader[2925] NSColor: 0 
09-02-24 2:07:35 PM Data Loader[2925] NSFont: 0 
09-02-24 2:07:35 PM Data Loader[2925] movie: 0 
09-02-24 2:07:35 PM Data Loader[2925] startTrim: 0 
09-02-24 2:07:35 PM Data Loader[2925] audioProperties: 0 
09-02-24 2:07:35 PM Data Loader[2925] placement: 0 
09-02-24 2:07:35 PM Data Loader[2925] effects: 0 
09-02-24 2:07:35 PM Data Loader[2925] startOffset: 0 
09-02-24 2:07:35 PM Data Loader[2925] data: 0 
09-02-24 2:07:35 PM Data Loader[2925] titleName: 0 
09-02-24 2:07:35 PM Data Loader[2925] duration: 0 
09-02-24 2:07:35 PM Data Loader[2925] titleNibName: 0 
09-02-24 2:07:35 PM Data Loader[2925] anchorTargetOffset: 0 
09-02-24 2:07:35 PM Data Loader[2925] aspectRatio: 0 
09-02-24 2:07:35 PM Data Loader[2925] enabled: 0 
09-02-24 2:07:35 PM Data Loader[2925] anchorItem: 0 
09-02-24 2:07:35 PM Data Loader[2925] text1: 0 
09-02-24 2:07:35 PM Data Loader[2925] graphicsColor: 0 
09-02-24 2:07:35 PM Data Loader[2925] text2: 0 
09-02-24 2:07:35 PM Data Loader[2925] NSParagraphStyle: 0 
09-02-24 2:07:35 PM Data Loader[2925] NSColor: 0 
09-02-24 2:07:35 PM Data Loader[2925] NSFont: 0 
09-02-24 2:07:35 PM Data Loader[2925] movie: 0 
09-02-24 2:07:35 PM Data Loader[2925] startTrim: 0 
09-02-24 2:07:35 PM Data Loader[2925] audioProperties: 0 
09-02-24 2:07:35 PM Data Loader[2925] placement: 0 
09-02-24 2:07:35 PM Data Loader[2925] effects: 0 


I also tried decodeObjectForKey:@"root" with no success.

The documentation shows no methods such as keyEnumerator or keys, etc.  Does anyone know of any way to figure out what keys exist in a NSKeyedUnarchiver?  Or, is there any sort of documentation out there on the NSKeyedArchiver output format?  I have been able to make virtually no sense from it at all, the data is not structured at all, it is all in one big flat array... What a mess!

Also, this is the data that is successfully decoded.  You can see the pointers to the objects of all the classes I made myself.  These are the objects that are not being properly instantiated.

    
09-02-24 1:53:31 PM Data Loader[2858] Data:

{
    editInfo =     {
        aspectRatio = 1.333333;
        automaticDuckingBackground = 0;
        automaticDuckingContent = 0;
        automaticTransitions = 0;
        editList =         (
                        {
                audioProperties =                 {
                };
                duration = 1925;
                effects =                 {
                };
                movie = <RCMovie: 0x164b50>;
                placement =                 {
                    placementMode = 1;
                    placementStart = [0.5 0.5 1];
                };
                startTrim = 1;
            },
                        {
                attached = 1;
                duration = 15;
                transition = <Transition: 0x164a90>;
            },
                        {
                blackFrame = 1;
                duration = 285;
                movie = <ProjectMovie: 0x165010>;
                startTrim = 15;
                stillOffset = 1200;
            }
        );
        frameRate = 30;
        guid = "8D0FC4FF-D95D-49B3-A988-7C9ECE4214CC";
        largestMovieWidth = 1920;
        masterVolume = 1;
        projectVersion = 7;
        projectVersionCompatibleBackTo = 4;
        songs =         (
        );
        stillDuration = 4;
        stillDurationMode = 0;
        stillMovieTimeScale = 600;
        stillPlacement = 2;
        titleFadeDuration = 0.5;
        titles =         (
            <TitleEditItem: 0x164640>,
            <TitleEditItem: 0x162b10>
        );
        transition = <Transition: 0x164a90>;
        transitionDuration = 0.5;
        transitionDurationMode = 0;
        videoPlacement = 1;
        voiceOvers =         (
        );
    };
}


Thanks.

----

If you drop an archive file on the Property List Editor utility, it will allow you to explore all the objects in the archive. A lot of it is housekeeping, but you'll be able to see all the keys that you can decode and the object types they are associated with. --GC

----

I've looked at the data in Property List Editor as much as I could, and all of the keys that it LOOKS like should exist, simply do not (according to NSKeyedUnarchiver).

For example:

    

@implementation TitleEditItem

- (id)initWithCoder:(NSCoder *)decoder
{
	self = [super init];
	NSLog(@"Decoding: %@", [self className]);
    NSLog(@"Has: %d", [decoder containsValueForKey:@"startOffset"]);
    NSLog(@"Has: %d", [decoder containsValueForKey:@"data"]);
    NSLog(@"Has: %d", [decoder containsValueForKey:@"duration"]);
    NSLog(@"Has: %d", [decoder containsValueForKey:@"localOffset"]);
    NSLog(@"Has: %d", [decoder containsValueForKey:@"tracksAnchor"]);
    NSLog(@"Has: %d", [decoder containsValueForKey:@"enabled"]);
    NSLog(@"Has: %d", [decoder containsValueForKey:@"anchorItem"]);
    NSLog(@"Has: %d", [decoder containsValueForKey:@"titleName"]);
    NSLog(@"Has: %d", [decoder containsValueForKey:@"anchorTargetOffset"]);
    NSLog(@"Has: %d", [decoder containsValueForKey:@"titleNibName"]);
    NSLog(@"Has: %d", [decoder containsValueForKey:@"frame"]);
    NSLog(@"Has: %d", [decoder containsValueForKey:@"aspectRatio"]);
	return self;
}

- (void)encodeWithCoder:(NSCoder *)encoder
{
	
}

@end



All that results in is:

    

2009-03-11 Mar 11, 2009 1:55:13 PM Data Loader[79840] Decoding: TitleEditItem 
2009-03-11 Mar 11, 2009 1:55:13 PM Data Loader[79840] Has: 0 
2009-03-11 Mar 11, 2009 1:55:13 PM Data Loader[79840] Has: 0 
2009-03-11 Mar 11, 2009 1:55:13 PM Data Loader[79840] Has: 0 
2009-03-11 Mar 11, 2009 1:55:13 PM Data Loader[79840] Has: 0 
2009-03-11 Mar 11, 2009 1:55:13 PM Data Loader[79840] Has: 0 
2009-03-11 Mar 11, 2009 1:55:13 PM Data Loader[79840] Has: 0 
2009-03-11 Mar 11, 2009 1:55:13 PM Data Loader[79840] Has: 0 
2009-03-11 Mar 11, 2009 1:55:13 PM Data Loader[79840] Has: 0 
2009-03-11 Mar 11, 2009 1:55:13 PM Data Loader[79840] Has: 0 
2009-03-11 Mar 11, 2009 1:55:13 PM Data Loader[79840] Has: 0 
2009-03-11 Mar 11, 2009 1:55:13 PM Data Loader[79840] Has: 0 
2009-03-11 Mar 11, 2009 1:55:13 PM Data Loader[79840] Has: 0 



And according to the documentation, the issue isn't the fact that I'm not using the keys in the right order, because apparently NSKeyedUnarchiver can decode in any order.  I've also tried doing things like:

    

- (id)initWithCoder:(NSCoder *)decoder
{
	self = [super init];
    NSDictionary *dict = [[NSDictionary alloc] initWithCoder:decoder];
	return self;
}



I thought maybe I had to manually pass the decoder to another class.  That didn't work either...  One would think that since containsValueForKey; is a method, that they could probably create an allKeys: or similar method, since they're obviously looping through some list of keys and checking them, and since you can decode out of order, that array must be available at any time...

Any more ideas??

Thanks,
--PercyHanna

