---
layout: page
title: InstanceCounting
---

I'm working on a framework with 25-30 classes, and right now I'm in a testing phase, writing nice test cases for most of the classes. While doing this I discover lots of memory leaks, and fix them. However, I only discover the leaks as a result of testing the other code, so I'm probably missing some. In one of the cases, I wrote a simple addition to one of my classes, so that it incremented a counter whenever it was alloc'ed and decremented the same counter when it was dealloc'ed, effectively giving me a counter telling me how many instances of this class that were around at any given time. Then I calculated the difference between the number of instances before the tested code, and compared that to the number after (making sure that I had allocated a new autorelease pool for that code, and released it before the comparation). This was very handy for detecting where the memory leaks actually occured.

Now, I want to do this with any class. I know that there are two utilities called OmniObjectMeter and ObjectAlloc, that can do something like this. But what I want to do is to include this check in my testcases, which means that running ObjectAlloc or whatever is not an option. What I want to do is call a function that gives me the current instance count of a named class (any class, not just my own), then execute some code and compare the new result from that function with the old in an assertion or something. Then I can select the test target in my project, type command-r and just wait for the message "All tests passed".

So, how do I do this? Apple and Omni knows how, so it must be possible.

--TheoHultberg/Iconara

----

You could try using MethodSwizzling to add to NSObject's allocWithZone:/retain/release methods. -- KritTer

----

Nice idea, I will try this out and report back.
-- TheoHultberg/Iconara

----

OK, here's my implementation: (link removed, see below instead)

Example
    
// wrap the counting in an autorelease pool, so that we can
// release any autoreleased objects before counting
NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];

int count;

IXStartInstanceCounting();

count = IXInstaceCountForClass( [Foo class] );

doSomethingThatMightLeakMemory();

// important! if the pool is not released, the instance count
// will be higher because of any autoreleased objects in the pool
[pool release];

// check so that the number of instances of Foo is the same as
// before the function call that might leak (in this case zero)
NSCAssert( diff == IXInstaceCountForClass( [Foo class] ), @"Leaking memory" );

IXEndInstanceCounting();


You can also use the function IXAllInstanceCounts() to get a dictionary with all the classes that have been allocated since the count started, and the number of instances of them. 

I have decided on having a start and end function since the code that does the counting shouldn't be on always (it's a lot of uneccesary instructions if you're not interested in the counting).

-- TheoHultberg/Iconara

----

*Can you rewrite your code to automatically report *all* memory leaks? Having to do it per class could well miss mistakes, e.g. where you used a temporary NSString and forgot to release it properly. -- KritTer*

----

Yeah, sure, I'll do that. There are problems with it though, NSString isn't just NSString, it's reported as NSCFString and other kinds as well (because of NSString being a ClassCluster), but what the hell, it's no big deal. --TheoHultberg/Iconara

----

OK, now, if you call the function IXEndInstanceCountingAndCheckLeaks() (hey, that was a long name) it'll trow an exception (IXMemoryLeakException) warning you that there are alloc'ed objects that have not been dealloc'ed. The message will tell you which classes and how many instances there are left. The userInfo dictionary contains all classes and their counts.
-- TheoHultberg/Iconara (who loves when he can solve his own problems with just a nudge in the right direction from someone, cheers KritTer)

*No problemo. In the interest of global harmony...or...something...anyway, I'm posting the code you made up here. We can then scour it and refactor it, and it's up even if your server perishes :) Take it down of course if you object. -- KritTer*

Good idea --TheoHultberg

----

**IXInstanceCounting.h**

    
/*
 * @header         IXInstanceCounting
 * @abstract       This is a collection of functions to enable instance counting
 * @discussion     Instance counting is a way to count how many instances of a
 *                 class that exist in the scope of a program at a given time.
 *                 It can be used to discover and track memory leaks, or just to
 *                 discover how memory management in a piece of code works.
 *
 *                 Instance counting is not reference counting, the difference is
 *                 that the instance count tells you how many instances of a class
 *                 (that is, how many objects) exist, whereas reference counting
 *                 tells you how many references to a single instance (object)
 *                 there are.
 *
 *                 Basic usage:
 *                 Cocoa does not support instance counting by itself, so we have
 *                 to strap it on somehow. This is done by calling the function
 *                 IXStartInstanceCounting before the code you wish to check.
 *                 Call the funtion IXEndInstanceCounting to end the instance
 *                 counting. In between the two calls you can check the current
 *                 number of instances of a class by calling the function
 *                 IXInstanceCountForClass with the class that you're interested
 *                 in as an argument.
 *
 *                 The instance count will be relative to when counting started.
 *                 If you create twenty instances of NSString, and then start
 *                 instance counting, IXInstanceCountForClass will still report
 *                 that the instance count of NSString was zero.
 *
 *                 It's a good idea to create an autorelease pool around the code
 *                 you're checking, since any autoreleased objects otherwise would
 *                 report as leaking when the instance counting ended.
 *
 *                 Example:
 *                     NSAutoreleasePool *pool;
 *                     DOMDocument *document;
 *                     int documents, elements, attributes, texts, pis;
 *                 	
 *                     IXStartInstanceCounting();
 *                 	
 *                     pool = [[NSAutoreleasePool alloc] init];
 *                 	
 *                     document = [[DOMBuilder defaultBuilder] buildFromFile:@"XML/build.xml"];
 *
 *                     documents  = IXInstanceCountForClass( [DOMDocument class] );
 *                     elements   = IXInstanceCountForClass( [DOMElement class] );
 *                     attributes = IXInstanceCountForClass( [DOMAttribute class] );
 *                     texts      = IXInstanceCountForClass( [DOMText class] );
 *                     pis        = IXInstanceCountForClass( [DOMProcessingInstruction class] ); 
 *
 *                     NSLog( 
 *                         @"There are %d documents, %d elements, %d attributes, %d texts and %d processing instructions",
 *                         documents, elements, attributes, texts, pis
 *                     );
 *                 
 *                     [pool release];
 *                 	
 *                     IXEndInstanceCounting();
 *
 *                 Advanced usage:
 *                 The function IXEndInstanceCountingAndCheckLeaks can be used to end
 *                 the instance counting and throw an exception if any class has
 *                 an instance count of more than one. I use this feature in my unit 
 *                 testing to test complex features that I suspect contain memory leaks.
 *                 I end the instance counting with IXEndInstanceCountingAndCheckLeaks, 
 *                 which throws an exception if any class has a positive instance count.
 *                 I catch this exception and trigger an assertion failure, which is
 *                 picked up by my unit testing framework, and reported as a test failure.
 *
 *                 Example:
 *                     NSAutoreleasePool *pool;
 *                     
 *                     IXStartInstanceCounting();
 *                     
 *                     pool = [[NSAutoreleasePool alloc] init];
 *                     
 *                     // test code goes here
 *
 *                     [pool release];
 *                     
 *                     NS_DURING
 *                         IXEndInstanceCountingAndCheckLeaks();
 *                     NS_HANDLER
 *                         NSAssert ( NO, @"Memory leak!" );
 *                     NS_ENDHANDLER
 *
 *                 Notes:
 *                 IXStartInstanceCounting will raise an exception if called again without
 *                 first having called IXEndInstanceCounting or IXEndInstanceCountingAndCheckLeaks.
 *                 
 *                 IXInstanceCountForClass and IXAllInstanceCounts will raise an
 *                 exception if no counting is in progress.
 *
 *                 The instance counts of some classes can sometimes be negative,
 *                 this is only natural, since instances created before the start
 *                 of the instance counting can be deallocated within the scope of
 *                 the instance counting.
 *
 *                 The dictionary returned by IXAllInstanceCounts has class objects
 *                 as keys, and instances of the class IXCount as values. Call
 *                 the method -value on the IXCount instance to get the instance count.
 *                 The reason why the values are not NSNumbers is that NSNumbers are
 *                 immutable, and the counts are updated very frequently, you don't
 *                 want to create an throw away massive amounts of objects like that,
 *                 especially when doing memory leak checking...
 *
 *                 There is more documentation on the functions below.
 *                 
 */

#import <Foundation/Foundation.h>

/*!
 * @extern         IXMemoryLeakException
 * @abstract       Raised when a memory leak is discovered, 
 *                 see IXEndInstanceCountingAndCheckLeaks
 */
extern NSString *IXMemoryLeakException;

/*!
 * @extern         IXInvalidStateException
 * @abstract       Raised if the instance counting is forced in to an
 *                 ivalid state, i.e when start is called twice without
 *                 an end in between.
 */
extern NSString *IXInvalidStateException;

/*!
 * @function       IXStartInstanceCounting
 * @abstract       Call to start instance counting
 * @discussion     Did I mention that you have to call this method 
 *                 before you want instance counting to begin?
 *
 *                 It is recommended that you create a local autorelease
 *                 pool just after calling this function, and release
 *                 that pool just before ending the counting. By doing this
 *                 you ensure that any autoreleased objects are released
 *                 properly and don't look like unreleased objects in the counts.
 *
 *                 If this method is called again, before the counting has ended
 *                 an exception of type IXInvalidStateException will be raised.
 */
void IXStartInstanceCounting( void );

/*!
 * @function       IXEndInstanceCounting
 * @abstract       Call when instance counting is no longer needed, 
 * @discussion     After this function has been called, all counts are lost,
 *                 but since the counting is not free, neither in space
 *                 nor time, it can be a good idea to end the counting.
 *
 *                 This function has no effect if instance counting has not
 *                 started (i.e IXStartInstanceCounting has not been called).
 */
void IXEndInstanceCounting( void );

/*!
 * @function       IXEndInstanceCountingAndCheckLeaks
 * @abstract       Does what IXEndInstanceCounting does and raises
 *                 an exception of type IXMemoryLeakException
 *                 if there was any class that had a positive 
 *                 instance count.
 * @discussion     The message of the exception will contain the names
 *                 of the classes that were leaked. The user info
 *                 dictionary contains all classes and their instance counts.
 */
void IXEndInstanceCountingAndCheckLeaks( void );

/*!
 * @function       IXInstanceCountForClass
 * @abstract       Get the number of unique instances there has
 *                 been created since instance counting started.
 * @discussion     If the number is negative, it means that
 *                 some objects have been alloc'ed before the instance 
 *                 counting started.
 *
 *                 If instance counting has not been started this function
 *                 raised an exception of type IXInvalidStateException 
 *                 is raised.
 *
 */
int IXInstanceCountForClass( Class class );


/*!
 * @function       IXAllInstanceCounts
 * @abstract       Returns a dictionary containing all the counts for all classes
 * @discussion     The keys of the dictionary are the classes, the value
 *                 is an object of type IXCount.
 *
 *                 If instance counting has not been started this function
 *                 raised an exception of type IXInvalidStateException 
 *                 is raised. 
 */
NSDictionary *IXAllInstanceCounts( void );

/*!
 * @class          IXCount
 * @abstract       A simple counter class.
 * @discussion     Dictionaries take only objects as values, and the instance
 *                 counting scheme needs a counter, so instead of creating
 *                 and destroying lots of NSNumbers just to increment
 *                 and decrement the instance count of a class, IXCount is used.
 *                 You can increment, decrement and get the value. Everything you need.
 */
@interface IXCount : NSObject {
	int i;
}

/*!
 * @method         increment:
 * @abstract       Increments the value by one, equivalent to ++.
 */
- (void)increment;

/*!
 * @method         decrement:
 * @abstract       Decrements the value by one, equivalent to --.
 */
- (void)decrement;

/*!
 * @method         value:
 * @abstract       Returns the current value as an int.
 */
- (int)value;

@end


----

**IXInstanceCounting.m**

    
#import "/usr/include/objc/objc-class.h"

#import "IXInstanceCounting.h"


void _IXICSwizzle(void);
void _IXMethodSwizzle(Class aClass, SEL orig_sel, SEL alt_sel);

NSString *IXMemoryLeakException   = @"IXMemoryLeakException";
NSString *IXInvalidStateException = @"IXInvalidStateException";

static NSMutableDictionary *IXInstanceCounts = nil;


@interface NSObject ( InstanceCounting ) 

- (id)ixic_init;

- (void)ixic_dealloc;

@end


void IXStartInstanceCounting( ) {
	if ( IXInstanceCounts == nil ) {
		[IXInstanceCounts release];
		IXInstanceCounts = [[NSMutableDictionary alloc] init];
	
		_IXICSwizzle();
	} else {
		[NSException raise:IXInvalidStateException
		            format:@"Instance counting already in progress"];
	}
}

void IXEndInstanceCounting( ) {
	if ( IXInstanceCounts != nil ) {
		_IXICSwizzle();
	
		[IXInstanceCounts release];
	
		IXInstanceCounts = nil;
	}
}

int IXInstanceCountForClass( Class class ) {
	if ( IXInstanceCounts != nil ) {
		IXCount *i = [IXInstanceCounts objectForKey:class];
	
		return [i value];
	} else {
		[NSException raise:IXInvalidStateException
		            format:@"Instance counting not in progress"];
		return nil;
	}
}

NSDictionary *IXAllInstanceCounts( ) {
	if ( IXInstanceCounts != nil ) {
		return IXInstanceCounts;
	} else {
		[NSException raise:IXInvalidStateException
		            format:@"Instance counting not in progress"];
		return nil;
	} 
}

void IXEndInstanceCountingAndCheckLeaks( ) {
	if ( IXInstanceCounts != nil ) {
		NSException *exception;
		NSMutableDictionary *leaks = [NSMutableDictionary dictionary];
		NSMutableString *leakedClasses = [NSMutableString string];
		NSEnumerator *e;
		Class c;
		
		_IXICSwizzle();

		e = [IXInstanceCounts keyEnumerator];
		
		if ( c = [e nextObject] ) {
			IXCount *count = [IXInstanceCounts objectForKey:c];
			
			if ( [count value] > 0 ) {
				[leakedClasses appendFormat:@"%@ (%d)", NSStringFromClass(c), [count value]];
			}
		}
		
		while ( c = [e nextObject] ) {
			IXCount *count = [IXInstanceCounts objectForKey:c];
		
			if ( [count value] > 0 ) {
				[leakedClasses appendFormat:@", %@ (%d)", NSStringFromClass(c), [count value]];
				[leaks setObject:count forKey:c];
			}
		}
		
		if ( [leaks count] > 0 ) {
			NSString *reason = [NSString stringWithFormat:@"Memory leak detected: %@", leakedClasses];
		
			exception = [NSException exceptionWithName:IXMemoryLeakException 
			                                    reason:reason
			                                  userInfo:IXInstanceCounts];
		} else {
			exception = nil;
		}

		[IXInstanceCounts release];
	
		IXInstanceCounts = nil;
		
		[exception raise];
	} else {
		[NSException raise:IXInvalidStateException
		            format:@"Instance counting not in progress"];
	} 
}

void _IXICSwizzle( ) {
	_IXMethodSwizzle([NSObject class], @selector(init),   @selector(ixic_init));
	_IXMethodSwizzle([NSObject class], @selector(dealloc), @selector(ixic_dealloc));
}

void _IXMethodSwizzle(Class aClass, SEL orig_sel, SEL alt_sel) {
	/*
	 * IXMethodSwizzle
	 * This function exchanges one implementation of
	 * a method with another, it is used by IXStartInstanceCounting
	 * and IXEndInstanceCounting to enable or disable the instance
	 * counting scheme.
	 * Kudos to Jack Nutting & Kritter for this code. See
	 * http://www.cocoadev.com/index.pl?MethodSwizzling	
	 * for more information.
	 */
	 
	Method orig_method = nil, alt_method = nil;

	// First, look for the methods
	orig_method = class_getInstanceMethod(aClass, orig_sel);
	alt_method = class_getInstanceMethod(aClass, alt_sel);

    // If both are found, swizzle them
    if ((orig_method != nil) && (alt_method != nil)) {
		char *temp1;
		IMP temp2;

		temp1 = orig_method->method_types;
		orig_method->method_types = alt_method->method_types;
		alt_method->method_types = temp1;

		temp2 = orig_method->method_imp;
		orig_method->method_imp = alt_method->method_imp;
		alt_method->method_imp = temp2;
	}
}

@implementation IXCount

- (id)init { self = [super init]; i = 0; return self; }

- (void)increment { i++; }

- (void)decrement { i--; }

- (int)value { return i; }

- (NSString *)description { return [NSString stringWithFormat:@"IXCount[value=%d]", i]; }

@end


@implementation NSObject ( InstanceCounting )

- (id)ixic_init {
	if ( [self class] != [IXCount class] ) {
		IXCount *i = [IXInstanceCounts objectForKey:[self class]];
	
		if ( i == nil ) {
			i = [[IXCount alloc] init];
			[IXInstanceCounts setObject:i forKey:[self class]];
		}
		
		[i increment];
	}
	
	return [self ixic_init];
}

- (void)ixic_dealloc {
	if ( [self class] != [IXCount class] ) {
		IXCount *i = [IXInstanceCounts objectForKey:[self class]];
	
		if ( i == nil ) {
			i = [[IXCount alloc] init];
			[IXInstanceCounts setObject:i forKey:[self class]];
		}
	
		[i decrement];
	}
	
	[self ixic_dealloc];
}

@end



----

*Changes:*


* Moved an import to the .m --Theo
* Fixed a bug in IXEndInstanceCountingAndCheckLeaks, the exception would be raised before the instance counts dictionary was emptied. --Theo
* Added state checks, will raise an exception if instance counting is already in progress (currently only supports one counting at a time, could perhaps be extended to allow for several counts at the same time). --Theo
* Added loads of documentation to the header. --Theo

