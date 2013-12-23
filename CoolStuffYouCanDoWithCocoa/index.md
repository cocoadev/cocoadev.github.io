---
layout: page
title: CoolStuffYouCanDoWithCocoa
---

To give an overall sense of the power of Cocoa, add in some of the cool stuff you can do with Cocoa that would be rather more difficult with more common systems. Please provide some clear examples with your comments.

----

Thanks to some of the handy functions in Apple's Objective-C runtime headers (<objc/objc-class.h> and <objc/objc-runtime.h>), you can easily create arrays of all the loaded classes at runtime and all the method names for a particular class.  This is runtime introspection.

The following two methods could be placed into a category on NSArray:

    
+(NSArray *)arrayWithStringsForClassList
{
	int i = 0;
	int numClasses = 0, newNumClasses = objc_getClassList(NULL, 0);
    Class *classes = NULL;
	NSMutableArray *classesArray = [[NSMutableArray alloc] init];
	
    while (numClasses < newNumClasses)
	{
        numClasses = newNumClasses;
        classes = realloc(classes, sizeof(Class) * numClasses);
        newNumClasses = objc_getClassList(classes, numClasses);
    }
	
	for(i = 0; i < numClasses; i++)
		[classesArray addObject:NSStringFromClass(classes[i])];
	
    free(classes);
	
	return [classesArray autorelease];
}

+(NSArray *)arrayWithStringsForMethodsOfClass:(Class)theClass
{
	void *iterator = 0;
	struct objc_method_list *mlist;
	int i;
	NSMutableArray *selectorArray = [[NSMutableArray alloc] init];
	
	while(mlist = class_nextMethodList([theClass class], &iterator))
		for(i=0; i<mlist->method_count; ++i)
			[selectorArray addObject:NSStringFromSelector(mlist->method_list[i].method_name)];
	
	free(iterator);
	free(mlist);
	return [selectorArray autorelease];
}



This is advanced stuff, but it really gives you a sense of the power that Objective-C and Cocoa give you.

-- RobRix

AWESOME!

Actually, the above example is not correct - each methodList has an array of methods. The example above only prints the first one. here is the corrected example:

    
	void *iterator;
	struct objc_method_list *mlist; 
	int i;

 	// must start with this initialed   
	iterator = nil;
	
	mlist = (struct objc_method_list *)class_nextMethodList( [NSLayoutManager class], &iterator );
	
	while(mlist != nil) {
		NSLog(@"Methods in this chunk %d", mlist->method_count);
		for(i=0; i<mlist->method_count; ++i) {
			NSLog(@"--Method Name:%@ types %s", NSStringFromSelector(mlist->method_list[i].method_name), mlist->method_list[i].method_types);
		}
		mlist = (struct objc_method_list *)class_nextMethodList( [NSLayoutManager class], &iterator );
	}


David

*Fixed the code inline. Incidentally, why are you "correcting" it by replacing working code with NSLog<nowiki/>s?*

----

This is not Cocoa specific, but thanks to the GCC provided by apple you can mix and match 2 of the most popular programming languages (C and C++) and one of the best programming languages (Objective-C) in ONE SOURCE FILE! There are no lack of libraries that are compatible with Cocoa! I guess .NET and CLR try to provide something almost like this, but it does not leverage 20 + years of programming libraries.

    
// Source.h
struct STKFilterCore;
@interface STKFilter : NSObject
{
    struct STKFilterCore * core;
}
- (void) processSoundFile:(NSString*)path;
@end

// Source.mm
#import "Source.h"

#include <boost/shared_ptr.hpp>
#include <boost/shared_array.hpp>
using namespace boost;

#include <Filter.h> // From STK, a C++ library.
#include <sndfile.h> // A great soundfile loading C library.

struct STKFilterCore
{
    shared_ptr<Filter> filter;
    // Constructor, destructor, copy, ie. a full C++ class!
};

@implementation STKFilter
- (id) init
{
    if(self= [super init]) {
        try { core = new STKFilterCore; } catch(...) { 
            NSLog(@"Failed to create core, aborting.");
            [self release];
            return nil;
        }
    }
    return self;
}
- (void) dealloc
{
    delete core;
    [super dealloc];
}
- (void) processSoundFile:(NSString*)path
{
    // Error checking left out for compactness...
    SF_INFO sndFileInfo_IN, sndFileInfo_OUT;
    memset(&sndFileInfo_IN, 0, sizeof(SF_INFO));
    SNDFILE * sndFileIn  = sf_open([path fileSystemRepresentation], SFM_READ, &sndFileInfo_IN);

    sndFileInfo_OUT = sndFileInfo_IN;
    SNDFILE * sndFileOut = sf_open(path stringByAppendingString:@"_OUT"] fileSystemRepresentation], 
        SFM_WRITE, &sndFileInfo_OUT);	

    unsigned amtLeft = sndFileInfo_IN.frames*sndFileInfo_IN.channels;
    const unsigned chunkSize = 512;
    shared_array<float> data(new float[chunkSize]);
    while(amtLeft) {
        const unsigned thisSize = MIN(chunkSize, amtLeft);
        sf_read_float(sndFileIn, data.get(), thisSize);
       
        core->filter->tick(([[StkFloat*)data.get(), thisSize);

        sf_write_float(sndFileOut, data.get(), thisSize);
        amtLeft -= thisSize;
    }
	
    sf_close(sndFileIn);
    sf_close(sndFileOut);
}
@end


This is slightly simplified and incomplete, but the full file only needs about 100 more lines of code. Hundreds of programming hours designing Filters, figuring out file formats, all leveraged in minutes, put into a transparent Cocoa object that can be used by any Cocoa newbie!

Jeremy Jurksztowicz

----

The     -cString was getting pretty off-topic for this page. Moved to StringWithCString.

----

One cool thing you can do with Cocoa is this:
    [[NSArray array] objectAtIndex:100];

----
FadeAViewAndItsContents is kind of neat, even if it could use some refinement. And there's CCDImageCategory, which you can use to make cells draw into an NSImage instead of an NSView like they expect to.

