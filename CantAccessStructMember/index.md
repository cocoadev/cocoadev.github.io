---
layout: page
title: CantAccessStructMember
---

I believe the struct member **stuList** somehow doesn't exist.  I can't figure out why not, and it is likely a simple thing but I am completely frustrated tying to make it go.

    
	typedef struct {                                             // Struct to hold a class
		int classID;
		NSString *classSec, *className;
		NSMutableArray *stuList, *stuData;       // The problem variable.  Supposed to grow using addObject to hold strings
	} Classes;
	
	int i, j, perNum;
	NSString *line, *str;
	NSArray *parts;
	
	Classes classList[7];                                      // Array of 7 Class structures

        NSOpenPanel *openPanel = [NSOpenPanel openPanel];

	for (i = 0; i < 7; i++) {
		classList[i].classID = 0;
	}

        if ([openPanel runModalForTypes: [NSArray arrayWithObjects: @"grd",@"GRD",NULL]]) {
		NSString *theWholeEnchilada = [NSString stringWithContentsOfFile: [openPanel filename]]; 
		NSScanner *myScanner = [NSScanner scannerWithString: theWholeEnchilada], *myOtherScanner;
		
		while ([myScanner scanUpToString: @"\n" intoString: &line ]){ 
			parts = [line componentsSeparatedByString: @"\t"];
			if ( parts objectAtIndex: 0] isEqualToString: @"N300"] ) {
				// 3000 = first TA course number
				// 2000 = first Special Ed course number
				if ( [[parts objectAtIndex: 2] intValue] > 2000 || [[parts objectAtIndex: 2] intValue] < 3000 ) {
					classList[ [[parts objectAtIndex: 4] intValue] ].classID = [[parts objectAtIndex: 2] intValue];
					classList[ [[parts objectAtIndex: 4] intValue] ].classSec = [parts objectAtIndex: 3];
					myOtherScanner = [[[NSScanner scannerWithString: [parts objectAtIndex: 5]];
					[myOtherScanner scanUpToString: @" -" intoString: &str];
					str = [str stringByAppendingString: @")"];
					if ( [str characterAtIndex: 0] == '(' ) str = [str substringFromIndex: 3];
				}
			}
			if ( parts objectAtIndex: 0] isEqualToString: @"N320"] ) {
				if ( [ [parts objectAtIndex: 2] intValue ] > 2000 || [ [parts objectAtIndex: 2] intValue ] < 3000 ) {
					for ( i = 5 ; i < [parts count] ; i++ ) {
						perNum = [ [parts objectAtIndex: 4] intValue ];
						[ classList[perNum].stuList addObject: [ parts objectAtIndex: i] ];         // This should add an element, right?
						j = [ classList[perNum].stuList count ];                                                 // But j gets 0 here -- what gives?
					}
				}
			}
		}
        }




*There's no evidence anywhere in your code that you've created an [[NSMutableArray for     stuList to point to. Do you have, elsewhere in your code, something resembling     classList[perNum].stuList=[[NSMutableArray alloc] init]? If not, you may be finding that     j==0 because you're calling a method on a nil object (the result of a method call to a nil object is nil by definition in ObjectiveC).
*I also have a...philosophico-stylistic question for you. Why are you using C structs to organise your data when you could be using Cocoa's wonderful modelling classes everywhere? Why not replace your Class struct with an NSDictionary or a custom class?

----

Yes, you're never alloc/initting your mutable array.

If you're hellbent on using structs, instead of something NSObject-derived ( which I think is your best option ) you should write a constructor function, something like:

    

void Classes_init( Classses *instance )
{
  instance->classID = 0;
  instance->stuList = [[NSMutableArray alloc] init];
  instance->stuData = [[NSMutableArray alloc] init];
}

void Classes_destroy( Classes *instance )
{
  [instance->stuList release];
  [instance->stuData release];
}

//and then in your code
// the const int might only work in C++...

const int nClasses = 7;
Classes classList[ nClasses ];

NSOpenPanel *openPanel = [NSOpenPanel openPanel];

for (i = 0; i < nClasses; i++) {
  Classes_init( &classList[i]);
}



This is basically the kind of thing C programmers do when they need to do something object-oriented. Of course, they've got APIs like GObject to streamline it, but why bother when you could just have an NSObject version?

    

@interface ClassInfo : NSObject
{
  int classID;
  NSString *classSec, *className;
  NSMutableArray *stuList, *stuData;   
}

+(ClassInfo*) classInfoWithClassID: (int) classID;

- (void) setClassSec: (NSString*) aClassSec;
- (void) setClassName: (NSString*) aClassName;

- (NSString *) classSec;
- (NSString *) className;

... and so on

@end

@implementation ClassInfo

- (id) init {
  if ( self = [super init] )
  {
    classID = 0;
    stuList = [[NSMutableArray alloc] init];
    stuData = [[NSMutableArray alloc] init];
}

- (void) dealloc {
  [stuList release];
  [stuData release];
}

+(ClassInfo*) classInfoWithClassID: (int) classID
{
  ClassInfo *ci = [[[ClassInfo alloc] init] autorelease];
  ci->classID = classID;
}

- (void) setClassSec: (NSString*) aClassSec
{
  [classSec autorelease];
  classSec = [aClassSec retain];
}

... and so on

@end



Sure, it's a little more code. But think of it this way: your program/data is obviously modeled around this Classes struct. It an be construed then that it is important. Therefore, you might as well spend a little time wrapping it in a class with a concise public interface, so that you can run test-units on the Classes object itself and be reasonably assured of its robustness. If you take the struct approach you'll open yourself up to a lot of room for errors, since every time you assign a string or array you'll have to explicitly release/retain -- which means that if you assign in ten different places in your program, you've got ten opportunities to screw up.

Let me put it this way: there's no reason not to use structs in Objective-C. But, my rule is this: any structure which requires some sort of memory management, be it a malloc-ed array or NSStrings or NSMutableDictionary or whatnot, should be refactored into a class, with memory management handled behind the public interface.

--ShamylZakariya

----

Having coded in C that was what I fell back on, but I can see that objC has some more nifty features that I need to learn about.  Thank you very much!

-- Charlie

----
Or, if you are comfortable with C++, you can fall back on that as well using Objective-C++. It is what I use for almost all of my internal code and it allows me to avoid "heap based" objects when I really want silly-fast stack bound objects.

